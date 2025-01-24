---
description: >-
  Read how to configure Nx-based targets in MergeQueue. Nx is a popular build
  framework for monorepos that identifies the targets that are affected by a
  change.
---

# nx based affected targets

[<mark style="color:blue;">Nx</mark>](https://nx.dev/) is a popular build framework for monorepos that focuses in simplicity. Like many other distributed build frameworks, the core concept is around identifying the targets that are affected in a change, and using a build cache. These same concepts can also be used for optimizing MergeQueue builds.

### GitHub Action

GitHub action might be the easiest way to fetch the affected targets and push that to Aviator API. Here’s an example of how you can setup such a GitHub Action. You can also find this code in action in [<mark style="color:blue;">nx-examples repository fork</mark>](https://github.com/aviator-co/nx-examples/blob/master/.github/workflows/aviator-targets.yml).

```yaml
name: Fetch Aviator targets

on:
  pull_request:

jobs:
  nextjs-build:
    runs-on: ubuntu-latest
    defaults:
      run:
         working-directory: .
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'yarn'
      - run: yarn install
      - run: |
          set -euo pipefail
          git fetch origin $GITHUB_BASE_REF
          PR_NUMBER=${{ github.event.number }}
          TARGETS_JSON=$(npx nx show projects --affected --base FETCH_HEAD --head HEAD --json)
          curl -X POST -H "Authorization: Bearer ${{ secrets.AVIATOR_API_TOKEN }}" \
          -H "Content-Type: application/json" \
          -d '{
            "action": "update",
            "pull_request": {
                "number": '"$PR_NUMBER"',
                "repository": {"name": "nx-examples", "org": "aviator-co"},
                "affected_targets": '"$TARGETS_JSON"'
            }
          }' https://api.aviator.co/api/v1/pull_request/
```

### A few things to note

* It is recommended to store the API access token as `AVIATOR_API_TOKEN` in [GitHub secrets.](https://docs.github.com/en/actions/security-guides/encrypted-secrets) or replace the secrets above
* There are two separate `action` for the `pull_request` API: `update` and `queue`. If you use `update` API, these affected targets information is sent to Aviator. A developer can then queue the PR asynchronously. If using the `update` API, you should call this GitHub action every time a new commit is added to the PR.
* Alternatively, you can submit this as a `queue` action when the PR is ready to be queued. In that case, the information is submitted to the Aviator MergeQueue while queueing the PR in the same step.
