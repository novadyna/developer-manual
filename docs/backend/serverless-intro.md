## Introduction to the Serverless Framework

---

Nova's backend utilizes the [Serverless Framework](https://www.serverless.com/){target=_blank}.

It's a toolkit for declaring and deploying cloud resources.

Most people use it more for managing a RESTFUL API(this is our main use case), less so for actual
resource creation(although you very well can).

Creating API Gateway resources manually(even with `CloudFormation` and/or `AWSCDK`)\
is a cumbersome process involving many interdependent resource declarations.

`Serverless` simplifies that process by a lot.

The core of a **serverless** project is the `serverless.yml`. It is the main declaration file in
which you'd declare your endpoints(or other resources).

This is a simplified version of the `serverless.yml` in use for `nwv2-api-admin-iam`:

```yaml
service: ${file(./.env.${self:provider.stage}.json):projectName}-api-admin-iam

provider:
  name: aws
  runtime: nodejs12.x
  stage: ${opt:stage, 'dev'}
  region: ${opt:region, 'ap-southeast-1'}
  profile: ${opt:profile, 'nwv2Admin'}
  environment:
    AdminTable: ${file(./.env.${self:provider.stage}.json):adminTable}
    EmailFrom: ${file(./.env.${self:provider.stage}.json):emailFrom}
  apiGateway:
    restApiId: ${file(./.env.${self:provider.stage}.json):restApiId}
    restApiRootResourceId: ${file(./.env.${self:provider.stage}.json):restApiRootResourceId}
  iamRoleStatements:
    - Effect: Allow
      Action:
        - dynamodb:*
      Resource: arn:aws:dynamodb:${self:provider.region}:*:*

functions:
  sharedAdminAuthorizer:
    handler: functions/admin-iam-authorizer.handler

  createUser:
    handler: functions/create-user.handler
    events:
      - http:
          path: admin-iam/users
          method: post
          authorizer:
            type: CUSTOM
            authorizerId:
              Ref: CustomAdminAuthorizer
          cors: true

  authenticate:
    handler: functions/authenticate.handler
    events:
      - http:
          path: admin-iam/authenticate
          method: post
          cors: true
```

Let's break it down bit by bit:

```yaml
service: ${file(./.env.${self:provider.stage}.json):projectName}-api-admin-iam
```

Name of the project, the actual CloudFormation stack deployed will be built off of this, if you
modify the service name and redeploy, it will be deployed to a new stack instead of updating the old
one.

```yaml
provider:
  environment:
    AdminTable: ${file(./.env.${self:provider.stage}.json):adminTable}
    EmailFrom: ${file(./.env.${self:provider.stage}.json):emailFrom}
```

This is where environment variables are declared. Anything under `provider.environment` will be
placed into `process.env` for runtime access.

What you see there is `Serverless`'s variable resolution syntax.

[Reference](https://www.serverless.com/framework/docs/providers/aws/guide/variables/){target=_blank}
.

Assuming stage = `dev`, `${file(./.env.${self:provider.stage}.json):emailFrom}` will be resolved to
the value of the `emailFrom` attribute inside `.env.dev.json` under project root.

```yaml
apiGateway:
  restApiId: ${file(./.env.${self:provider.stage}.json):restApiId}
  restApiRootResourceId: ${file(./.env.${self:provider.stage}.json):restApiRootResourceId}
```

This property is usually not required, since `Serverless` creates a new `API Gateway` from scratch
if one is not provided.

But since we create our `API Gateway` resource with the `AWSCDK`, we provide the IDs here to
let `Serverless` know that we want to build our endpoints under an existing API Gateway rather than
a new one.

```yml
functions:
  authenticate: aws
    handler: functions/authenticate.handler
    events:
      - http:
          path: admin-iam/authenticate
          method: post
          cors: true
```

Attributes under `functions` will be treated as a `Lambda` function and deployed as such.

`handler` points it towards a file that contains the function code.

`events` are triggers that can invoke this function, `http` means this is triggered by the
invocation of an endpoint.

For a full reference to all available attributes in `serverless.yml`, please refer to the 
[official serverless.yml reference](https://www.serverless.com/framework/docs/providers/aws/guide/serverless.yml){target=_blank}.
