---
description: >-
  Learn how to configure ChangeSets in MergeQueue using our step-by-step guide.
  With this feature, you can create a list of PRs that need to be merged
  together.
---

# How to Configure ChangeSets

The [<mark style="color:blue;">ChangeSets feature</mark>](../concepts/changesets/) allows you to create a list of PRs to be merged together.

### Step 1: Connect your repositories to Aviator

If you haven't connected your GitHub repositories to Aviator yet, you can do so by clicking the "Add Repos" button on the [<mark style="color:blue;">Repositories page</mark>](https://mergequeue.com/github/repos).

### Step 2: Configure ChangeSet settings

There are a few settings you can configure on the [<mark style="color:blue;">ChangeSet settings page</mark>](https://mergequeue.com/changeset/settings)<mark style="color:blue;">:</mark>

* **Enable comments for new PRs** - When this setting is turned ON, Aviator will post a comment on each PR to link it with a new or existing ChangeSet.
* **Require global CI validation** - Continue to [<mark style="color:blue;">global CI validation</mark>](../concepts/changesets/global-ci-validation.md) for more details.
* **Automatically add PRs with the same branch name to a ChangeSet** - When turned on, Aviator will automatically add your PR to the matching ChangeSet when your changes share the same branch name. A new ChangeSet will be created if it doesn't exist.

### Step 3: Configure webhooks

If you are using global CI validation, you must also make sure the webhook is configured. See the [<mark style="color:blue;">Webhooks</mark>](https://docs.aviator.co/api/reference/webhooks) section to configure your webhooks.
