---
icon: bell
description: >-
  Read documentation for Aviator AttentionSet, inspired by Gerritt, A dashboard
  for developers to track pull request reviews from GitHub.
---

# AttentionSet

Inspired by[ <mark style="color:blue;">Gerrit</mark>](https://gerrit-review.googlesource.com/Documentation/user-attention-set.html), AttentionSet is a dashboard for developers to track Pull Request reviews from GitHub. It is intended to track the code reviews back-and-forth between the author and the reviewers. Using AttentionSet, developers can easily unblock changes that are waiting on their action.

<figure><img src="../.gitbook/assets/AttentionSetattentionset (1) (1).png" alt=""><figcaption><p>AttentionSet dashboard</p></figcaption></figure>

## Attention

The dashboard identifies who should be paying attention to a PR. For instance, when the author prepares a PR and sends it to the reviewers, the reviewers have the attention; and when the reviewers respond back to the PR (commenting or approving), the author gets the attention back. The dashboard also displays the reason why an attention is assigned to you or who is your PR’s attention assigned to.

Every PR can have one or more people who have the attention, a PR may also occasionally have no attention, although this should be rare for a PR that is in open / ready for review state. A merged PR will not have anyone’s attention.

Attention is typically managed automatically, but it can also be toggled [<mark style="color:blue;">manually</mark>](manually-change-attention.md) as needed.

## Getting started

If you are a new Aviator user, you will first need to install the Aviator app on your GitHub repository. This is typically done as part of the initial setup wizard. You can choose to start with any of MergeQueue or FlexReview workflows to begin the setup.

<figure><img src="../.gitbook/assets/Screenshot 2024-03-06 at 3.44.52 PM.png" alt=""><figcaption><p>Aviator setup wizard</p></figcaption></figure>

Once the initial setup is complete, you will be prompted to link the GitHub account. This step is necessary so we verify your GitHub username that is used to track your attention.

<figure><img src="../.gitbook/assets/Screenshot 2024-03-06 at 3.45.28 PM.png" alt=""><figcaption><p>AttentionSet - Link GitHub account</p></figcaption></figure>

\
