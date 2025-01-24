---
description: >-
  Get guidelines on how to pause / unpause queues via API for MergeQueue.
  Pausing allows you to control whether PRs are merged to a specific branch.
---

# Pause / Unpause Queues via API

[<mark style="color:blue;">Pausing a queue</mark>](../concepts/paused-queues.md) allows you to control whether PRs should be merged to a certain branches.

### Pausing all queues in a repository

You can prevent all PRs from merging in a repository. A repository can be paused from the [<mark style="color:blue;">repositories page</mark>](https://app.aviator.co/github/repos) or using the API:

```bash
curl -X POST \
  -H "Authorization: Bearer <aviator_token>" \
  -H "Content-Type: application/json" \
  -d '{"org": "org_name", "name": "repo_name", "paused": true }' \
https://api.aviator.co/api/v1/repo/
```

### Pausing queues via the base branch

Pausing the queue associated with a base branch can be helpful if you want to stop all merges while a release is being prepared. In this case, PRs that target all other base branches will continue merging regularly but the PRs associated with the specific base branch will be paused. Base branch pausing capability is only supported via the API:

```bash
curl -X POST \
    -H "Authorization: Bearer <aviator_token>" \
    -H "Content-Type: application/json" \
    -d '{ "pattern": "release-*", "repository": {"org": "aviator", "name": "av-demo-release"}, "paused": true, "paused_message": "This release branch has been paused."}' \
https://api.aviator.co/api/v1/branches
```

The base branch API also takes an optional parameter called paused\_message that provides an additional message in the GitHub comments for a PR when a queue is paused.

### Checking if a queue is paused

You can also use a GET request to find the current pause status of all branches:

```bash
curl -H "Authorization: Bearer <aviator_token>" \
    -H "Content-Type: application/json" \
    -d '{ "repository": {"org": "aviator", "name": "av-demo-release"}}' \
https://api.aviator.co/api/v1/branches
```
