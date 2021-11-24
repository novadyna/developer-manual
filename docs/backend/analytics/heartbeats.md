## Heartbeats

---

This is the hottest path on the Nova platform, every viewer triggers this endpoint every 30 seconds,
on every page.

This means that if there are 4 thousand viewers currently using the platform, heartbeat will be
triggered 8000 times a minute.

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
