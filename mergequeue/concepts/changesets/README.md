---
description: >-
  Read an overview of the ChangeSets feature. It allows you to create a list of
  PRs to be merged together, which is helpful for merging a large number of PRs.
---

# ChangeSets

The ChangeSets feature allows you to create a list of PRs to be merged together. This is useful if you have a large number of PRs that need to be merged together (e.g., coordinated changes to a web frontend, iOS app, and Android app). ChangeSets can group PRs within the same repository or across multiple repositories.

You can create a ChangeSet by clicking the "Create New" button on the ChangeSet page. This will create an empty ChangeSet.

Add a PR to this ChangeSet by finding the PR you want to add (search by GitHub user, or by Repository name), and click the "Add to ChangeSet" button next to it. Repeat the process to add as many PRs as you like to this ChangeSet.

To remove a PR from a ChangeSet, click the "Remove" button next to the PR name.

Once ready, you can merge the ChangeSet by clicking the "Merge" button on the page. Aviator will then validate the approvals and status checks for all the PRs in the ChangeSet and merge them together. You can optionally specify additional validation requirements that must be met before the whole ChangeSet is merged. If validation fails, the ChangeSet will be marked as failed and none of the PRs will be merged.



{% hint style="info" %}
Checkout the [<mark style="color:blue;">how-to-guide for configuring ChangeSets</mark>](../../how-to-guides/configuring-changesets.md) in your repository.
{% endhint %}

