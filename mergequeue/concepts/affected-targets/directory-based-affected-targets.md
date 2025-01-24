---
description: >-
  Get setup instructions for automated and manually specified targets. Learn how
  to manually define targets based on your directory structure in our guide.
---

# Directory-Based Affected Targets

If you don't use Bazel like systems to auto-generate the affected targets, you can instead manually define targets based on your directory structure. There are two ways to do it.

### Automated (preferred)

A GitHub action can be created to automatically assign affected targets to reach PR.

```yaml
name: Aviator Queue PR
on:
  pull_request:
    types: [ labeled ]
jobs:
  path_board:
    if: ${{ github.event.label.name == 'av-ready' }}
    runs-on: ubuntu-latest
    steps:
    - uses: dorny/paths-filter@v2
      id: filter
      with:
        filters: |
          workflows:
            - '.github/workflows/*'
          billing:
            - billing/.*
          tests:
            - tests/*
          other:
            - *.txt
    - run: |
        TARGETS=${{ toJSON(steps.filter.outputs.changes) }}
        PR_NUMBER=${{ github.event.number }}
        curl -X POST -H "Authorization: Bearer ${{ secrets.AVIATOR_API_KEY }}" \
          -H "Content-Type: application/json" \
          -d '{
          "action": "queue",
          "pull_request": {
              "number": '"$PR_NUMBER"',
              "repository": {"name": "testrepo", "org": "ankitjaindce"},
              "affected_targets": '"$TARGETS"'
          }
        }' https://api.aviator.co/api/v1/pull_request/
```

A few things to note:

* We use [<mark style="color:blue;">path-filters action</mark>](https://github.com/dorny/paths-filter) to capture all the file path changes in the given PR.
* You may also want to store the API access token as `AVIATOR_API_KEY` in [<mark style="color:blue;">GitHub secrets.</mark>](https://docs.github.com/en/actions/security-guides/encrypted-secrets) or replace the secrets above
* `filters` are the various directory packages that you can define as various affected targets. It accepts glob format and you can define more than 1 path per affected target. You may also optionally specify this in a separate file. Read more on the path-filters action [<mark style="color:blue;">documentation</mark>](https://github.com/dorny/paths-filter).
* This will queue the PR in Aviator. You can also want to setup a similar GH action to dequeue when the label is removed.
* This label `av-ready` should be separate from the label defined in Aviator’s configuration.

### Manually specify targets

If you prefer to do this manually, you can also specify the affected targets as a Slash command comment in your PR. The targets can be represented as a comma separated list in the `/aviator merge` command:

```
/aviator merge --targets=frontend,api,android
```

Although Manual approach is much simpler, it is prone to human errors and is a less preferred approach.
