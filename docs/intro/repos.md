## Repositories

---

Backend API (API Gateway stacks managed with [Serverless Framework](https://www.serverless.com/framework/docs){target=_blank}:

- `nwv2-api-admin-iam`
    - Serverless stack
    - Admin authentications
    - Contains authorizer shared by other admin stacks
    - **MUST BE DEPLOYED FIRST!**
- `nwv2-api-admin`
    - Core admin APIs
    - Creating and modifying `channels`, `events`, `sessions`, and other platform resources.
- `nwv2-api-analytics`
    - What the name suggests
- `nwv2-api-event-interaction`
    - APIs pertaining to interactive elements of an event:
        - Polls, Q & As, Surveys, anything that involves end users submitting things. 
- `nwv2-api-message-queue`
    - Asynchronous batch processing that involves lambda fan-outs with SQS
- `nwv2-api-viewer-auth`
    - Viewer authentications
    - **MUST BE DEPLOYED FIRST!**
        - Before or after `nwv2-api-admin-iam` 
- `nwv2-api-viewer-events`
    - The server side rendering part of Novaweb platform
    - Extremely convoluted, very hard to read even with intimate knowledge of business logic.
    - Consult Louis.
- `nwv2-api-lib`
    - Submodule shared by the other repos
