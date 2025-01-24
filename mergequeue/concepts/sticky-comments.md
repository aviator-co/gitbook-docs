---
description: >-
  Aviator provides sticky comments to show the PR status within GitHub. Check
  out the core comments that are generated to better understand when you can see
  them.
---

# Sticky Comments

In addition to the dashboard, Aviator provides sticky comments to give you the status of a PR within GitHub.

Aviator posts a comment when a PR is opened, and will automatically update the comment as the PR moves through its lifecycle. This is a great way to stay up to date and identify any reasons that may have dequeued your PR or is preventing your PR from merging.

<figure><img src="../../.gitbook/assets/Screen Shot 2023-04-24 at 12.31.23 PM.png" alt=""><figcaption></figcaption></figure>

Having a sticky comment makes it easy for developers to find the status of the PR in one place, and also reduces noise if you set up notifications for new GitHub comments.

{% hint style="info" %}
You can also [<mark style="color:blue;">customize these sticky comments</mark>](../how-to-guides/customize-sticky-comments.md).
{% endhint %}

The following are some of the important of comments that are generated when comments are enabled:

* **CI is taking longer than expected.** - This is only applicable in Parallel mode. It can happen if the draft PR CI is completed but the original PR is still pending CI status. If the PR is `stuck`, then the comment will also provide info on when the PR will be dequeued and which CI(s) are stuck.
* **This PR is queued for merge (queue position: \<number>).** - Reports successful queuing along with the current position of the PR in the queue. Info on pending status checks will also be provided.
* **PR failed to merge with reason: \<reason>** - Reports a PR getting dequeued with one of the reasons as described above. It may also contained information about the associated draft PR if relevant along with additional debug info.
* **CI for this PR is waiting to be queued \<note>. CI for this PR will run automatically when the queue catches up.**
* **This PR was merged using MergeQueue.**
* **This PR was merged manually.**
* **This PR was closed without merging. If you still want to merge this PR, re-open it.**
* **This PR is not ready to merge (currently in state \<status>: \<description>).**

#### Stacked PRs

The following comments are only applicable if you are using Stacked PRs.

* **Waiting for the previous PR in this stack to be queued (or merged) before queuing this PR.** - This is only applicable if using stacked PRs.
* **This PR is stacked on top of \<PR#>. Changes from this PR can be reviewed separately and changes to the parent PR will automatically be synced into this PR.**
* **Next PR in this stack: \<dependent PR#s>**

#### ChangeSets

The following comments are only applicable if you are using ChangeSets.

* **PR has been added to the ChangeSet.**
* **MergeQueue ChangeSet actions: \[View Changeset dashboard]\<dashboard\_url>**
