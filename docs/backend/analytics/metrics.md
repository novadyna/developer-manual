## Metrics

---

The following is a list of available metrics obtainable from this stack:

To understand what kinda metric they provide, it is recommended to go into the functions one by one,
and read them.

```yaml
getSessionActiveViewerCount:
  path: analytics/events/{eventId}/sessions/{sessionId}/viewers/active-count
  method: get
getEventActiveViewerCount:
  path: analytics/events/{eventId}/viewers/active-count
  method: get
heartbeatReport:
  path: analytics/events/{eventId}/heartbeat-report
  method: post
getViewerCount:
  path: analytics/events/{eventId}/viewers/count
  method: get
getAllTimeActiveViewerCount:
  path: analytics/events/{eventId}/viewers/all-time-active-count
  method: get
getLiveEventStats:
  path: analytics/events/{eventId}/statistics/live
  method: get
getLiveSessionStats:
  path: analytics/events/{eventId}/sessions/{sessionId}/statistics/live
  method: get
getEventStats:
  path: analytics/events/{eventId}/statistics
  method: get
getSessionStats:
  path: analytics/events/{eventId}/sessions/{sessionId}/statistics
  method: get
getEventOverview:
  path: analytics/events/{eventId}/overview
  method: get
getEventTotalAttendedViewerCount:
  path: analytics/events/{eventId}/viewers/total-attended-count
  method: get
getSessionTotalAttendedViewerCount:
  path: analytics/events/{eventId}/sessions/{sessionId}/viewers/total-attended-count
  method: get
getEventMaxConcurrentViewerCount:
  path: analytics/events/{eventId}/viewers/max-concurrent-count
  method: get
getSessionMaxConcurrentViewerCount:
  path: analytics/events/{eventId}/sessions/{sessionId}/viewers/max-concurrent-count
  method: get
getEventTotalLoginCount:
  path: analytics/events/{eventId}/viewers/total-login-count
  method: get
getSessionViewingStats:
  path: analytics/events/{eventId}/sessions/{sessionId}/viewers/viewing-stats
  method: get
```
