---
description: Permissions & explanations
---

# GitHub App Permissions

The Aviator app requests a set of permissions on your GitHub repositories that you choose to connect with Aviator. While Aviator tries to request as few permissions as it needs to function, GitHub only allows us to request fairly broad groups of permissions. We’re committed to your privacy and security, so Aviator only uses the subset of permissions it needs to do its job.

## Repository Permissions

### Administration (read only)

This permission includes read-only access to repository settings, teams, and collaborators.

Aviator uses this permission in order to access a repository’s branch protection rules. Aviator will not (and cannot) edit any settings on your GitHub organization or repository.

### Checks (read & write)

This permission includes access to checks on code (such as GitHub actions and other integrations like CircleCI).

Aviator uses this permission to examine the status checks of your commits, branches, and pull requests. Aviator uses this information to determine when pull requests should be allowed to merge.

### Code/contents (read & write)

This permission includes access to repository contents, commits, branches, downloads, releases, and merges.

Aviator uses this permission to download the Aviator configuration file if you’ve added it to your repository.

Additionally, Aviator uses write permissions to update branches that are created and managed by Aviator as well and to perform actions explicitly requested by users (e.g., the `/aviator sync` command can be used to update a branch on demand).

Aviator may also access temporarily source code references in order to perform certain operations that are not supported directly on GitHub (such as rebasing pull requests). These actions are not enabled by default.

### Commit statuses (read only)

This permission includes access to commit statuses.

Aviator uses this permission to read the status information from individual commits or branches.

### Issues (read & write)

This permission includes access to issues and related comments, assignees, labels, and milestones.

Aviator uses this permission in order to read and write issue comments. Due to limitations with the GitHub API (pull requests and issues are deeply interlinked), we need this permission in addition to the _Pull Requests_ permission in order to listen to comments on pull requests (such as `/aviator sync` and other commands).

### Metadata (read only)

This permission includes access to search repositories, list collaborators, and access repository metadata.

Aviator is required to request this permission (it is mandatory for all GitHub apps). It does not include access to any privileged information about the repository.

### Pull requests (read & write)

This permissions includes access to pull requests and related comments, assignees, labels, milestones, and merges.

Aviator uses this permission to view, update, and merge pull requests which are managed by Aviator (e.g., when adding the ready-to-merge label to a pull request that should be placed into the queue).

### Workflows (read & write)

This permission includes ability to add or update an existing workflow.

Aviator uses this permissions to be able to merge pull requests that contain changes in directory `.github/workflows/`. Without this permission, Aviator may return an error when trying to queue or merge that particular pull request.

## Organization Permissions

### Members (read only)

This permission includes access to organization, and team membership.

Aviator uses this permission to confirm membership of users. This allows for actions to be restricted to only certain teams or organization members.

## Further reading

For a detailed explanation of what actions are enabled why the permissions above, see the [<mark style="color:blue;">GitHub developer docs for app permissions</mark>](https://docs.github.com/en/rest/overview/permissions-required-for-github-apps). Aviator does not use every single permission that it is granted (since GitHub only allows app developers to choose broad buckets of permissions like “administration” rather than just “branch protection rules”).
