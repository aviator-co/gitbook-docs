---
description: >-
  Read answers to the common questions users may face when using Aviator
  MergeQueue.
---

# MergeQueue

<details>

<summary>I have labeled the PR but it’s not merging.</summary>

There could be several reasons why the bot is not merging your PR.

* Check your Dashboard to see whether the queue is active or paused. If the queue is paused it will not pick up any changes.
* Check if all your CI statuses are completed (no required status is still pending).
* Check if there is no other PR ahead of your PR that is pending merge.
* Go to the [<mark style="color:blue;">GitHub app page</mark>](https://github.com/apps/mergequeue) and verify that the app is still authorized to your repository. If not, you may need to reconnect the app.

Once you have verified the above status, open your web app, and look at the Open and Blocked Queue. If your PR is in the Blocked Queue, there should be a reason listed for blocking as well. If none of these steps help, please contact us [<mark style="color:blue;">support@aviator.co</mark>](mailto:support@mergequeue.com)

</details>

<details>

<summary>My PR is not able to be merged because of GPG signature issues.</summary>

When a repository is configured to require commits to be signed, all commits in PRs must be signed with the committer's GPG key. MergeQueue will not merge PRs that contain unsigned commits if this option is enabled. If your commits are signed, but you're still facing this error, make sure that you've [<mark style="color:blue;">uploaded your GPG public key to GitHub</mark>](https://docs.github.com/en/authentication/managing-commit-signature-verification/adding-a-new-gpg-key-to-your-github-account).

To learn more about signing commits, see [<mark style="color:blue;">GitHub's "Signing commits" documentation</mark>](https://docs.github.com/en/authentication/managing-commit-signature-verification/signing-commits). If you need to sign commits that already exist, you'll need to interactively rebase your PR and sign the commits individually (`git rebase -i HEAD~N` for the relevant value of `N`, and, for every commit, add `exec git commit --amend --no-edit --no-verify --gpg-sign` below every `pick ...` line). You'll need to force push the branch or open a new pull request after doing this.

</details>
