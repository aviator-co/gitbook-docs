---
description: >-
  Learn how to configure GitHub Actions workflows to run only when certain files
  are changed. MergeQueue can identify the affected targets for the queue.
---

# GitHub Actions based Affected Targets

You can configure GitHub Actions workflows to run only when certain files are changed.

```yaml
on:
  push:
    paths:
      - '**.js'
```

Aviator MergeQueue can interpret this config to identify affected targets for the queue.

## Setup

In order to use GitHub Actions workflow to identify the affected targets, you need to create a new GitHub Actions workflow by using [<mark style="color:blue;">aviator-co/affected-targets-gha-action</mark>](https://github.com/aviator-co/affected-targets-gha-action).

Create following GHA workflow config.

```yaml
name: aviator-affected-targets

on:
  pull_request:

jobs:
  aviator-affected-targets:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: aviator-co/affected-targets-gha-action@v1
        with:
          aviator-token: ${{ secrets.AVIATOR_TOKEN }}
```

You can get the Aviator API token from [<mark style="color:blue;">https://app.aviator.co/integrations/api</mark>](https://app.aviator.co/integrations/api) and store it as a GitHub Actions secret.

Every time a PR is created, this workflow runs and detect which workflow is affected. This is sent to Aviator, and when this PR gets queued, it uses the affected targets reported by this action.
