---
description: >-
  Configuration guidelines for FlexReview. Core configuration properties that
  help you modify how FlexReview behaves: Enable / Disable, Approval Check, and
  more.
---

# Configuration

FlexReview allows you to onboard teams gradually. You can try FlexReview from one team, and expand it to others. You can configure FlexReview at the repository level and at the team level.

## Repository configs

### Enable / Disable

You can toggle FlexReview at the repository level. By enabling it at the repository level, it starts indexing Pull Requests for the reviewer suggestions.

### Author Allowlist

When FlexReview is enabled at the repository and owner teams enable FlexReview, it will start assigning a reviewer. You can further restrict this behavior based on the author. If this is configured, FlexReview will assign a reviewer if the PR author is in this group. This is by default `'*'`, which indicates there's no restriction on the PR author.

### Reviewer Exclusion

There are cases where you don't want to assign certain users. For example, managers can be in a team, but you might not want to assign them as a reviewer. You can add users and groups you do not want to assign as a reviewer.

### Ignore List

FlexReview will ignore all bot users, but in certain cases organizations may use a normal User account for automation purposes. To avoid not only assigning reviews to these users, but to ignore any actions they take on PRs add them to the Ignore List.

## GitHub Team configs

### Enable / Disable

You can toggle FlexReview at the GitHub team level. By enabling FlexReview at the team level and at the repository level, it will start assigning a reviewer based on the review assignment methods for PRs that modify the files that this team owns.
