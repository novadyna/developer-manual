## Post Deploy Setup

---

### Why?

After a successful deployment, there are 2 more things you'll need to do before the platform can
operate.

And they are:

- Initialize the database with a root user.
- Initialize the database with one set of html template for each `AuthPreset`
    - You probably have no idea what this means at this stage, but that's ok.
    - For now, just know that it has to be done.

### Pull the project

Both of these things can be done with the `nwv2-dev-tools` project, so go pull it now.

There's a submodule in it too, so please remember to initialize it.

Double check that `src/nwv2-api-lib` is not empty, and then we can move on to the next step.

### Env variables

The `nwv2-dev-tools` uses [dotenv](https://www.npmjs.com/package/dotenv){target=_blank}.

So the required env file is a key value pair instead of a `json`, and you'll need to create one
yourself.

First create a file and name it `.env`.

Paste this into the file:

```txt
Stage=
AWS_PROFILE=mwv2Admin
AWS_REGION=ap-southeast-1
BucketName=
UserImportBucket=
CloudfrontDomain=
ViewerTable=
ViewerSubmissionTable=
AdminTable=
LogTable=
ViewerHostname=
EmailFrom=
NovaBotToken=
ApiHostname=
RedisEndpoint=
MailchimpKey=
MailchimpServerPrefix=
MandrillKey=
```

You don't need all of the values for this to work, just these:

```txt
Stage=
AWS_PROFILE=mwv2Admin
AWS_REGION=ap-southeast-1
AdminTable=
```

`Stage` would be the name of your stage.
`AdminTable` would be the name of the corresponding table, in most cases it should be called
something like this:

`novaweb-<STAGE>-AdminTable`

Or you can also obtain the value from the regular `.env.<STAGE>.json` file.

### Actual Setup

Run:

```shell
# Make sure the submodule is pulled and inited!
npm install
```

After which the `postinstall` script should run.

Make sure the necessary env values are filled in. 

Run:

```shell
npm run-script setup-root-admin
```

And then run:

```shell
npm run-script init-default-templates
```

That's it!
