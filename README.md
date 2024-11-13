# Clean up after build

Clean up the work directory for self-hosted runners after they finish building.

## Example usage

```yaml
- uses: FraBle/clean-after-action@v2
```

> [!IMPORTANT]
> This must be run before any caching tasks, as cleanups are run in reverse order (and you do not want to clean up before the caching is saved).

A more full example:

```yaml
jobs:
  build:
    runs-on: onprem
    steps:
    - uses: FraBle/clean-after-action@v2
    - uses: actions/checkout@v4
    - uses: actions/cache@v4
    # ...
```

## Inputs

### `keep-git`

Set this to true to prevent the `.git ` folder from being deleted.

```yaml
- uses: FraBle/clean-after-action@v2
  with:
    keep-git: true
```

## What about Docker Actions?

If you use Docker Actions, files will be created by "root," and this action will fail to delete generated files.

This can be solved by forcing docker containers to run as the same user as the user that runs your self-hosted runner.
[To do this, you can use the user-remap feature in docker](https://docs.docker.com/engine/security/userns-remap/).

## Motivation

Several other actions " solve this problem" by deleting files as the action runs (instead of as a clean-up action).
The main problem with running cleanup as a build step is that it will run "before other cleanup steps."
This will effectively break things like caching and similar things that require files to be left when their cleanup runs.

A similar argument can be made for running cleanup before you build. This means the disk will eventually become full as more and more projects are built.
