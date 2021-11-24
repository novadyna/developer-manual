## Analytics Introduction

---

This is the stack that makes extensive use of the redis instance.

### The Basics

The heart of the analytics stack is the `heartbeat` function, its endpoint being:

```txt
analytics/events/{eventId}/heartbeat
```

This collects and logs whatever is required.

### Current Issues

This stack has a couple hard coded parameters that should probably be extracted into env variables.

They are:

- A redis instance endpoint
- A VPC with VPCEndpoints for DynamoDB, S3 and SQS
- 3 Subnets

However, compared to other preexisting architectural issues on our platform, this should be
relatively easy to remedy.
