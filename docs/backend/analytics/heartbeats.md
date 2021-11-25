## Heartbeats

---

This is the hottest path on the Nova platform, every viewer triggers this endpoint every 30 seconds,
on every page.

This means that if there are 4 thousand viewers currently using the platform, heartbeat will be
triggered 8000 times a minute.

### What does it do?

Loads a user's profile from DB, immediately cache it for faster access next time.

And then perform operations that enable the following metrics.

The Redis data sets that supports the following are detailed in the next chapter, these 2 chapters
should be read in parallel.

### Viewing Sessions

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

### Alive viewers count (Current viewing users)

We consider viewers that submitted a heartbeat within the past 65 seconds "active".

Logs a heartbeat(with user id and timestamp only), to a redis sorted set.

Logs another heartbeat to another set scoped to sessions if the url path indicates the viewer is
watching a session.

### Attendance

At the same time we also logs the viewer's ID to a redis set. We can then obtain a count of viewers
who has at least visited the platform once by issuing a cardinality command.

### Event Statistics

"Stats" is what we call an aggregated metric that involves viewer count grouped by user group and
country, this is admittedly not the best name for it.

They look something like this:

```json
{
  "viewerCount": 100,
  "timestamp": 1637661623070,
  "countByUserGroup": {
    "047417b2-4930-4ff0-a3a0-8572cc559005": 100
  },
  "countByViewingCountry": {
    "HK": 50,
    "US": 50
  }
}
```

Each record represents a 5 minutes time frame(this will become clear in later chapters concerning
aggregation).
