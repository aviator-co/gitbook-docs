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

## GitHub Team configs

### Enable / Disable

You can toggle FlexReview at the GitHub team level. By enabling FlexReview at the team level and at the repository level, it will start assigning a reviewer based on the review assignment methods for PRs that modify the files that this team owns.

## Enable / Disable

To enable FlexReview for a specific repository, select that repository from the dropdown and click the **Enable** button on the top right. This will start indexing the past pull request data but keep the repository in [<mark style="color:blue;">read-only mode</mark>](../concepts/read-only-mode.md). To disable it for a specific repository, click the **Disable** button from the FlexReview config page.

The same can also be done from the [<mark style="color:blue;">Repositories list</mark>](https://app.aviator.co/github/repos) page.

## CODEOWNERS

This configuration lets you specify a custom path for the `CODEOWNERS` file. One of the core advantages of using FlexReview is that it reduces the total number of reviewers required for each PR when compared to the default `CODEOWNERS` assignment. Using a non-default path ensures that GitHub will not auto-assign reviewers for a pull request, and lets FlexReview assign reviewers instead.

This configuration is completely optional. If your team does not use a `CODEOWNERS` file, or if you want to keep the default path for `CODEOWNERS`, you can keep this blank.

### Assignment conflict

When auto-assignment is enabled but GitHub is also assigning reviewers based on the `CODEOWNERS` file, Aviator will replace the default reviewers with the suggested ones. Note that depending on your team's configuration, this may still notify the default reviewers before they are removed from the reviewers list.
