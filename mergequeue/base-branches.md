# Base branches

By default, Aviator monitors all PRs in a repository and will enqueue all of them for merging. Internally, Aviator automatically manages separate queues for each base branch. So if you have a PR #1 that is targeting base branch main and PR #2 that is targeting base branch release/v3.4, then those two PRs will not conflict with each other.

You can explicitly specify base branches that Aviator should monitor, this way Aviator will ignore any PRs that target a different base branch. A base branch can also be specified as a glob pattern. Here’s an example config:

```
merge_rules:
  base_branches:
  - master
  - /release-*/
```
