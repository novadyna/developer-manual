## Repositories

---

Backend API (API Gateway stacks managed with [Serverless Framework](https://www.serverless.com/framework/docs){target=_blank}:

- `nwv2-api-admin-iam`
    - Serverless stack
    - Admin authentications
    - Contains authorizer shared by other admin stacks
    - **MUST BE DEPLOYED FIRST!**
- `nwv2-api-admin`
- `nwv2-api-analytics`
- `nwv2-api-event-interaction`
- `nwv2-api-message-queue`
- `nwv2-api-viewer-auth`
- `nwv2-api-viewer-events`
- `nwv2-api-lib`
    - Submodule shared by the other repos
