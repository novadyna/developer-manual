## Frequently Asked Questions

---

### I pulled the code, but the `nwv2-api-lib` folder is empty!

That's because it is a submodule.

[Git doc entry on submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules){target=_blank}.

Simply put, they are git's way of having nested repositories.

A submodule is not exactly a standalone repo, although it behaves like one.

To initialize and fetch a submodule, run the following in the parent repo's root folder:

```shell
git submodule update --init --recursive
```

It will initialize the submodule and reset it to the correct commit.

### Why is `npm install` looping forever?

In almost all API stacks, there is a npm `postinstall` declared inside `package.json`.

`postinstall` is an npm lifecycle that can be hooked into, it executes after every `npm install` command.

In our particular projects, the `postinstall` script `cd`s into a submodule folder and runs `npm install` there.

Now if a `package.json` file cannot be found, npm will search upwards recursively until one is found,
usually it'll find the one under project root, hence the loop.
