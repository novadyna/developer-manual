## Migrating Repositories

---

Migrating repos should be relatively painless, with one very important caveat.

Submodules.

If you are familiar with them. You can safely skip this article.

[What is a git submodule?](../backend/deployment/faqs.md)

A git submodule is declared inside a file named `.gitmodules` in the root folder.

This file is committed and is tracked by git.

The insides looks like this:

```txt
[submodule "functions/lib/nwv2-api-lib"]
	path = functions/lib/nwv2-api-lib
	url = ../nwv2-api-lib.git
```

The url param can take a relative path or a full path, some of the projects uses an absolute path, some relative.

An absolute path would look like this:

```txt
[submodule "functions/lib/nwv2-api-lib"]
	path = functions/lib/nwv2-api-lib
	url = git@gitlab.com:yellowvls/project/nova/nwv2-api-lib.git
```

You can probably imagine, if the library module is migrated, it will stop working, since the path would become incorrect.

It also probably wouldn't work if you use `http` instead of `git` protocol for fetching.

So in most cases, a relative path is recommended, unless the submodule is hosted on a different server.

As for relative paths, if the library module is not placed under a path with the same relativity, it won't work either.

So during migration, care should be taken to ensure submodules remain in the same location relative to their parent.

And that absolute submodule paths are updated to reflect the migration.
