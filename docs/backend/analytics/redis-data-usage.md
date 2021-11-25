## Redis Data Usage

---

This chapter explains the Redis data types utilized.

[Redis data types reference](https://redis.io/topics/data-types){target=_blank}.

Admittedly, keys are sometimes named rather haphazardly, some of these collections no longer need to exist
since their functionalities have been superseded by another.

### Sorted Sets

Non repeating collections of Strings, every item is associated with a score

Since every member in a sorted set is unique, only the latest is kept, older records of the same
user will get overwritten.

#### Viewing Sessions

- Viewing Session Start Time
    - Collection Key: <EVENT_ID>#<SESSION_ID(optional)>#<LOCATION>#ViewingSessionStart
    - Member Key: UserId
    - Score: Timestamp
- Viewing Session End Time
    - Collection Key: <EVENT_ID>#<SESSION_ID(optional)>#<LOCATION>#ViewingSessionEnd
    - Member Key: UserId
    - Score: Timestamp

#### Alive viewers count (Current viewing users)

Since ZSets are member unique, an accurate active viewer count can be obtained simply by issuing a
ZCOUNT with +inf for max and T minus alive threshold for min.

- User Event Heartbeats
    - Collection Key: <SESSION_ID>#Session#Heartbeat
    - Scoped to events
    - Member Key: UserId
    - Score: Timestamp
- User Session Heartbeats
    - Collection Key: <EVENT_ID>#Event#Heartbeat
    - Scoped to sessions
    - Member Key: UserId
    - Score: Timestamp

#### Event Statistics

These require a little more explanation. They are the statistics sets. There's one that's scoped to
events and another scoped to sessions.

Being a sorted set, members can only have a key field and an associated numerical score.

The score is the current timestamp, while the key is an encoded string containing the user's ID, IP
address they're accessing the site from together with country code, user groups they're members of.

- User Event Stats
    - Collection Key: <EVENT_ID>#Event#Statistics
    - Scoped to events
    - Member Key: <USERID>#<IPADDRESS>#(one or more <USERGROUPID>)#<COUNTRYCODE>
    - Score: Timestamp
- User Session Stats
    - Collection Key: <SESSION_ID>#Session#Statistics
    - Scoped to sessions
    - Member Key: <USERID>#<IPADDRESS>#(one or more <USERGROUPID>)#<COUNTRYCODE>
    - Score: Timestamp

These are poorly designed sets, and is a primary source of technical debt from inside the analytics
stack.

### Sets

Unique members, supports add, rmv, check for existence in O(1).

#### Attendance

- Event User Attendance
    - Collection Key: <EVENT_ID>#EventAttendedUsers
    - Scoped to events
    - Key: UserId
- Session User Attendance
    - Collection Key: <SESSION_ID>#SessionAttendedUsers
    - Grouped by SessionId
    - Key: UserId
