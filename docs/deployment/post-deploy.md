## Post Deploy Setup

---

After a successful deployment, there are 2 more things you'll need to do before the platform can
operate.

And they are:

- Initialize the database with a root user.
- Initialize the database with one set of html template for each `AuthPreset`
    - You probably have no idea what this means at this stage, but that's ok.
    - For now, just know that it has to be done.


Both of these things can be done with the `nwv2-dev-tools` project, so go pull it now.

There's a submodule in it too, so please remember to initialize it.
