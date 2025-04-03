---
description: >-
  View the reference to MergeQueue's actions for Pilot Automation Actions. Use
  these actions to manage and automate workflows and streamline other
  operations.
---

# Pilot Actions for MergeQueue

## `mergequeue.copy_pull_request`

**Using YAML actions:** `mergequeue.copy_pull_request`

**Using Pilot JavaScript:** `$mergequeue.copyPullRequest(...)`

Copy the HEAD sha of the pull request branch to the provided branch name.

### Inputs

This action expects an object argument with the following properties:

- `target` (`string | null`)

  - The target pull request.

    The value should be a string of the form
    `<repo owner>/<repo name>#<issue number>`.

    If unspecified, the pull request associated with the incoming event is used
    (if any).

- `branch_name` (`string`)
  - The name of the branch to copy the PR HEAD sha to.

## `mergequeue.instant_merge`

**Using YAML actions:** `mergequeue.instant_merge`

**Using Pilot JavaScript:** `$mergequeue.instantMerge(...)`

Instantly merge a pull request.

### Inputs

This action expects an object argument with the following properties:

- `target` (`string | null`)
  - The target pull request.

    The value should be a string of the form
    `<repo owner>/<repo name>#<issue number>`.

    If unspecified, the pull request associated with the incoming event is used
    (if any).

## `mergequeue.pause`

**Using YAML actions:** `mergequeue.pause`

**Using Pilot JavaScript:** `$mergequeue.pause(...)`

Pause a repository or branches within a repository.

### Inputs

This action expects an object argument with the following properties:

- `target` (`string | null`)

  - The target repository.

    The value should be a string of the form `<repo owner>/<repo name>`.

    If unspecified, the repository associated with the incoming event is used
    (if any).

- `branch_pattern` (`string | null`)

  - The base branch (or glob pattern) to pause.

    If given as a glob pattern, all known branches that match the glob pattern
    will be paused.

    If not specified, the entire repository is paused.

- `paused_message` (`string | null`)
  - A custom message that is added as a comment to pull requests while the pause
    is in effect.

## `mergequeue.queue`

**Using YAML actions:** `mergequeue.queue`

**Using Pilot JavaScript:** `$mergequeue.queue(...)`

Queue a pull request for merging.

### Inputs

This action expects an object argument with the following properties:

- `target` (`string | null`)
  - The target pull request.

    The value should be a string of the form
    `<repo owner>/<repo name>#<issue number>`.

    If unspecified, the pull request associated with the incoming event is used
    (if any).

## `mergequeue.synchronize_pull_request`

**Using YAML actions:** `mergequeue.synchronize_pull_request`

**Using Pilot JavaScript:** `$mergequeue.synchronizePullRequest(...)`

Synchronize a pull request with its base branch.

This uses the repository's configured merge method to update the pull request
with the latest commits from its base branch.

### Inputs

This action expects an object argument with the following properties:

- `target` (`string | null`)
  - The target pull request.

    The value should be a string of the form
    `<repo owner>/<repo name>#<issue number>`.

    If unspecified, the pull request associated with the incoming event is used
    (if any).

## `mergequeue.unpause`

**Using YAML actions:** `mergequeue.unpause`

**Using Pilot JavaScript:** `$mergequeue.unpause(...)`

Unpause a repository or branches within a repository.

### Inputs

This action expects an object argument with the following properties:

- `target` (`string | null`)

  - The target repository.

    The value should be a string of the form `<repo owner>/<repo name>`.

    If unspecified, the repository associated with the incoming event is used
    (if any).

- `branch_pattern` (`string | null`)
  - The base branch (or glob pattern) to unpause.

    If given as a glob pattern, all known branches that match the glob pattern
    will be un-paused.

    If not specified, the entire repository is unpaused.
