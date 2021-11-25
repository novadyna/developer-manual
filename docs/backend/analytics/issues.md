## Issues

---

In the analytics stack, currently, there are a couple issues worth keeping in mind.

### Redis does not scale

First of all, it makes heavy use of a Redis instance, and Elasticache instances does not auto scale(
or at least it was not setup), so this might one day become a bottleneck. Although bottlenecks for
current lambda execs and DynamoDB partition should be reached far sooner, therefore should the day
come when Redis can't handle the traffic, it should be the least of your worries.

### Redis Memory Size

There is currently no cache flushing mechanism setup for Redis. Again, his shouldn't be a problem in
the foreseeable future.

This should be even less of a problem once our redis instance creation is moved inside the CDK, and
are scoped to each stage.

Automatic(or manual) cache flush should be implemented, although this is not a current priority.

### Report and analytics makes use of cache records older than 5 minutes

Ideally, caches older than a certain threshold should be rendered useless, and is safe to purge. How
long that threshold should be is entirely dependent on application.

In our case, this is true for most records. However, some never expires, and metrics like event
overview, max concurrent viewer and total attended viewers relies on records throughout an event's
total lifetime(to different extents).

This makes cache purging difficult. Not impossible, but just hard.
