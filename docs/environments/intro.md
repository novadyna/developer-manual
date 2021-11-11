## Deployment Environments

---

### How many?

There are a total of 3 core stages, and they are:

- dev
- sandbox
- prod

### Where?

They are all located on `Nova`'s core account.

This is not ideal, and was a cheap and quick way to avoid heavy administrative overhead. AWS
recommends having separate accounts for staging and production.

In fact, latest AWS best practice guides recommends having separate accounts PER PROJECT. This is
certainly going to introduce admin overhead, and a much longer setup period. It would also require
automation tooling to properly manage. With the right CICD setup, it can be done, and the safety it
provides is probably worth it.

### The `dev` Stage(and any other personal stages)

These are for development purposes, database cleanse would sometimes happen unannounced(especially
if it's your own personal stage).

There are automation in place for nightly deploys.

Points to the latest `master` branch of every project.

### The `sandbox` Stage

This is what is usually called the QA environment.

It's for QA team's test drive of newly implemented features.

Team leads should be consulted before database cleanses take place.

Points to the latest `sandbox` branch.

### The `prod` Stage

The production environment.

Do not deploy unannounced. Do not do anything unannounced.

Talk with project managers to determine the best time for production deployment(preferably dates
with no impending events in the coming few days).

All hands on deck.
