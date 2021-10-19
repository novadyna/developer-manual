## Deployment

---

### Get Ready

Before we begin, ensure that you've obtained a copy of the ENV variables required for all API stacks.

They are named as such:

- `.env.<STAGE_NAME>.json`
- `.secrets.<STAGE_NAME>.json`

And they look somewhat like this:

![Screenshot](../assets/env-sample.png){: style="height:auto;width:auto"}

![Screenshot](../assets/secret-sample.png){: style="height:auto;width:auto"}

### Important Reminder

The API stacks can be deployed in any order **except for 2 of them**!

And they are:

- `nwv2-api-admin-iam` and
- `nwv2-api-viewer-auth`

They need to be deployed first, in no particular order.

The reason for that is they contain declarations to 2 authorizer functions that are imported and used
in almost all other stacks.

Relevant excerpts from `nwv2-api-admin-iam`'s `serverless.yml`:

![Screenshot](../assets/authorizer-declaration-1.png){: style="height:auto;width:auto"}

![Screenshot](../assets/authorizer-declaration-2.png){: style="height:auto;width:auto"}

### Deploy

We'll use `nwv2-api-admin-iam` as an example.

Change directory into the project root, run:

```shell
npm install
```

After which an npm `postinstall` script will be automatically triggered, the scripts simply goes into
a submodule's folder and run install from within, and then run a TypeScript transpiler.

Make sure both env files are placed under project root.

(The `nwv2-api-admin-iam` stack actually only requires one, but it doesn't hurt.)
