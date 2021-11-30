## Repositories

---

####Backend API
API Gateway stacks managed with [Serverless Framework](https://www.serverless.com/framework/docs){target=_blank}

- `nwv2-api-admin-iam`
    - Serverless stack
    - Admin authentications
    - Contains authorizer shared by other admin stacks
    - **MUST BE DEPLOYED FIRST!**
    - <https://gitlab.com/nova35/novaweb/v2/api/nwv2-api-admin>
- `nwv2-api-admin`
    - Core admin APIs
    - Creating and modifying `channels`, `events`, `sessions`, and other platform resources.
    - <https://gitlab.com/nova35/novaweb/v2/api/nwv2-api-admin>
- `nwv2-api-analytics`
    - What the name suggests
    - This project contains references to AWS resources not deployed by the CDK
        - See [Deployment](../backend/deployment/deployment.md) section for details.
    - <https://gitlab.com/nova35/novaweb/v2/api/nwv2-api-analytics>
- `nwv2-api-message-queue`
    - Asynchronous batch processing that involves lambda fan-outs with SQS
    - <https://gitlab.com/nova35/novaweb/v2/api/nwv2-api-msg-queue>
- `nwv2-api-event-interaction`
    - APIs pertaining to interactive elements of an event:
        - Polls, Q&As, Surveys, anything that involves end users submitting things. 
    - <https://gitlab.com/nova35/novaweb/v2/api/nwv2-api-event-interaction>
- `nwv2-api-viewer-auth`
    - Viewer authentications
    - Contains authorizer shared by other viewer stacks
    - **MUST BE DEPLOYED FIRST!**
        - Either before or after `nwv2-api-admin-iam` 
    - <https://gitlab.com/nova35/novaweb/v2/api/nwv2-api-viewer-auth>
- `nwv2-api-viewer-events`
    - The server side rendering part of Novaweb platform
    - Extremely convoluted, very hard to read even with intimate knowledge of business logic.
    - Consult Louis.
    - <https://gitlab.com/nova35/novaweb/v2/api/nwv2-api-viewer-event>
- `nwv2-api-lib`
    - Submodule shared by the other API repos
    - <https://gitlab.com/nova35/novaweb/v2/api/nwv2-api-lib>

####Front-end Clients
All client projects are using [VueJS](https://vuejs.org/){target=_blank} Framework and [BootstrapVue](https://bootstrap-vue.org/){target=_blank}.

- `nwv2-client-admin-panel`
    - Administrative Panel (a.k.a Console) and Mission Control
    - VueJS, Vue-Bootstrap
    - <https://gitlab.com/nova35/novaweb/v2/sites/nwv2-client-admin-panel>
- `nwv2-client-event-viewer`
    - Event Viewer
    - VueJS
    - <https://gitlab.com/nova35/novaweb/v2/sites/nwv2-client-event-viewer>
- `nwv2-client-extension-poll`
    - Poll Client
    - VueJS, Vue-Bootstrap
    - <https://gitlab.com/nova35/novaweb/v2/sites/nwv2-client-extension-poll>
- `nwv2-client-extension-survey`
    - Survey Client
    - VueJS, Vue-Bootstrap
    - <https://gitlab.com/nova35/novaweb/v2/sites/nwv2-client-extension-survey>
- `nwv2-client-lib`
    - Submodule shared by the other Client repos
    - <https://gitlab.com/nova35/novaweb/v2/sites/nwv2-client-lib>


####Utility Repos

- `nwv2cdk`
    - The installer of novaweb
    - It contains main script to launch the novaweb main stack, its fundamental infrastructure and resources on AWS, uses AWS Cloud Development Kit (CDK)
    - <https://gitlab.com/nova35/novaweb/v2/nwv2-cdk>
- `nwv2-dev-tools`
    - Connect to AWS resources from your local computer
        - With the exception of some EC2 resources like Redis, which can only be connected to from VPC
    - Suitable for quick tests and debugging without having to deploy code to a Lambda Function
    - <https://gitlab.com/nova35/novaweb/v2/utilities/nwv2-dev-tools>
- `novaweb-jmeter`
    - Automated API tests that ensures platform functionalities are up and running.
    - High but not 100% coverage, especially newer features.
    - Also contains scripts for stress testing purposes.
    - <https://gitlab.com/nova35/novaweb/v2/utilities/nwv2-jmeter-integration-tests>
- `nwv2-platform-init`
    - Contains scripts that initializes a newly deployed platform, mainly:
        - Initializes the console with a root admin user.
        - Copies default html templates into the database.
    - <https://gitlab.com/nova35/novaweb/v2/utilities/nwv2-platform-init>
- `developer-manual`
    - Source files of this documentation site
    - Uses Markdown and MkDocs
    - <https://gitlab.com/nova35/novaweb/v2/developer-manual>
