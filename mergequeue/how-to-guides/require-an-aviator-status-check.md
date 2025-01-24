---
description: >-
  Aviator supports a GitHub status check you can enable for PRs or add as a
  required check-in branch protection rules. Learn how to set up Aviator status
  checks.
---

# Require an Aviator Status Check

Aviator provides a [<mark style="color:blue;">GitHub status check</mark>](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/collaborating-on-repositories-with-code-quality-features/about-status-checks) that you can enable for your PRs. You can also add this status check as a required check in [<mark style="color:blue;">branch protection rules</mark>](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/managing-a-branch-protection-rule) to prevent developers from bypassing the merge queue. In the branch protection settings, you can find this check with the name: `aviator/checks`.

<figure><img src="../../.gitbook/assets/Screen Shot 2023-04-20 at 9.51.52 AM.png" alt=""><figcaption><p>aviator/checks as a GitHub status check</p></figcaption></figure>

The check typically contains similar details that you also see on the sticky comment.

<figure><img src="../../.gitbook/assets/Screen Shot 2023-04-20 at 9.51.07 AM.png" alt=""><figcaption></figcaption></figure>

This check is enabled by default but it can be disabled using the config setting below:

```
merge_rules:
  publish_status_check: false
```
