# Nova Developer Manual

---

This is a developer's manual for novaweb.live.

This manual assumes you are already familiar with the business domain. That is, the type of service
Nova provides, and the format in which it is provided.

This site is a work in progress, and should be updated continuously.

This
document's [git repository](https://gitlab.com/nova35/novaweb/v2/developer-manual){target=_blank}.

This manual is built
with [MkDocs](https://gitlab.com/nova35/novaweb/v2/developer-manual){target=_blank}, please refer to
the documentation on how to update an MkDocs site.

### Serve this site locally

Make sure you have `python` installed and `python` and `pip` is in your path.

Install MkDocs with `pip install mkdocs`.

Run `mkdocs serve`.

### Build

Run `mkdocs build`.

### Updating The Manual

There is no need to build and upload since this manual is hosted with Gitlab Pages.

CICD pipeline has been set up, every new commit on `master` will trigger a re-build.

To update the manual, simply commit, and push.

Refer to `.gitlab-ci.yml` under root to see what is being run.
