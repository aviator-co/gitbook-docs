---
description: >-
  Specify base branches Aviator should monitor, ignoring any PRs that target a
  different base branch. Learn how to configure base branches using an example
  here.
---

# Configure Base Branches

You can explicitly specify [<mark style="color:blue;">base branches</mark>](../concepts/base-branches.md) that Aviator should monitor, this way Aviator will ignore any PRs that target a different base branch. A base branch can also be specified as a glob pattern. Here’s an example config:

```
merge_rules:
  base_branches:
  - master
  - /release-*/
```
