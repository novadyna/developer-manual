## Heartbeats

---

This is the hottest path on the Nova platform, every viewer triggers this endpoint every 30 seconds,
on every page.

This means that if there are 4 thousand viewers currently using the platform, heartbeat will be
triggered 8000 times a minute.

### Redis Data

[Redis data types reference](https://redis.io/topics/data-types){target=_blank}.

#### Sorted Sets (Non repeating collections of Strings, every item is associated with a score).

Since every member in a sorted set is unique, only the latest is kept, older records of the same user
will get overwritten.

- Viewing Session Start Time
    - Grouped by EventId
    - Key: UserId
    - Score: Timestamp
- Viewing Session End Time
    - Grouped by EventId
    - Key: UserId
    - Score: Timestamp
- User Event Heartbeats
    - Grouped by EventId
    - Key: UserId
    - Score: Timestamp
- User Session Heartbeats
    - Grouped by SessionId
    - Key: UserId
    - Score: Timestamp

### What does it do?

1. Loads a user's profile from DB, immediately cache it.
    - DB or Redis Read x 1
2. Load
