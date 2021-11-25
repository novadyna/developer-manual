## Heartbeats

---

This is the hottest path on the Nova platform, every viewer triggers this endpoint every 30 seconds,
on every page.

This means that if there are 4 thousand viewers currently using the platform, heartbeat will be
triggered 8000 times a minute.

### What does it do?

Loads a user's profile from DB, immediately cache it for faster access next time.

And then perform operations that enable the following metrics.

#### Viewing Sessions

A viewing session represent a continuous period of time a user spends on the platform. A user can
have multiple viewing sessions.

1. Try to get a user's latest Viewing Session End Time
2. If there isn't one, or it has a timestamp that is considered expired(older than a threshold):
    - Set user's latest start time to Date.now()
    - Set their latest end time to now as well
    - Create a DB record
3. If one exists and isn't too old:
    - Get a user's viewing session start time
    - Set their latest end time to now
    - Update a DB record

#### Alive viewers count (Current viewing users)

We consider viewers that submitted a heartbeat within the past 65 seconds "active".

Logs a heartbeat(with user id and timestamp only), to a redis sorted set.

Logs another heartbeat to another set scoped to sessions if the url path indicates the viewer is
watching a session.

#### Event Statistics

"Stats" is what we call an aggregated metric that involves viewer count grouped by user group and
country, this is admittedly not the best name for it.

They look something like this:

```json
{
    "viewerCount": 100,
    "timestamp": 1637661623070,
    "countByUserGroup":
    {
        "047417b2-4930-4ff0-a3a0-8572cc559005": 100
    },
    "countByViewingCountry":
    {
        "HK": 50,
        "US": 50
    }
}
```

Each record represents a 5 minutes time frame(this will become clear in later chapters concerning aggregation).

### Redis Data

[Redis data types reference](https://redis.io/topics/data-types){target=_blank}.

#### Sorted Sets

Non repeating collections of Strings, every item is associated with a score

Since every member in a sorted set is unique, only the latest is kept, older records of the same
user will get overwritten.

These 2 are for keeping track of how long a user has stayed on a page.

- Viewing Session Start Time
    - Grouped by EventId
    - Key: UserId
    - Score: Timestamp
- Viewing Session End Time
    - Grouped by EventId
    - Key: UserId
    - Score: Timestamp

These 2 are for active user count.

Since ZSets are member unique, an accurate active viewer count can be obtained simply by issuing a
ZCOUNT with +inf for max and T minus alive threshold for min.

- User Event Heartbeats
    - Grouped by EventId
    - Key: UserId
    - Score: Timestamp
- User Session Heartbeats
    - Grouped by SessionId
    - Key: UserId
    - Score: Timestamp

There are 2 more sorted sets that are being used, and these require a little more explanation. They
are the statistics sets. There's one that's scoped to events and another scoped to sessions.

Being a sorted set, members can only have a key field and an associated numerical score.

The score is the current timestamp, while the key is an encoded string containing the user's ID, IP
address they're access the site from together with country code, user groups they're members of.

- User Event Stats
    - Grouped by EventId
    - Key: UserId
    - Score: Timestamp
- User Session Stats
    - Grouped by SessionId
    - Key: <USERID>#<IPADDRESS>#(one or more <USERGROUPID>)#<COUNTRYCODE>
    - Score: Timestamp

These are poorly designed sets, and is the primary source of technical debt from inside the
analytics stack.

#### Sets

Unique members, supports add, rmv, check for existence in O(1).

These 2 are for recording who has logged in at least once.

- Event User Attendance
    - Grouped by EventId
    - Key: UserId
- Session User Attendance
    - Grouped by SessionId
    - Key: UserId
