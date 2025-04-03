---
description: >-
  Access the Github integration reference for Pilot Automation Actions. Learn
  what actions you can perform on GitHub issues and get command samples in our
  guide.
---

# Pilot Actions for GitHub

## `github.add_comment`

**Using YAML actions:** `github.add_comment`

**Using Pilot JavaScript:** `$github.addComment(...)`

Add a comment to an issue or pull request.

### Inputs

This action expects either a single string argument or an object argument with
the following properties:

- `target` (`string | null`)

  - The target issue or pull request to apply the label to.

    This must be provided unless used where the target is implicitly defined by
    its context.

- `body` (`string`)
  - The contents of the comment.

## `github.add_label`

**Using YAML actions:** `github.add_label`

**Using Pilot JavaScript:** `$github.addLabel(...)`

Add a label to an issue or pull request.

If given as a string, the string is interpreted as the label to apply and the
target is implicitly determined by the calling context.

### Inputs

This action expects either a single string argument or an object argument with
the following properties:

- `target` (`string | null`)

  - The target issue or pull request to apply the label to.

    This must be provided unless used where the target is implicitly defined by
    its context.

- `label` (`string`)
  - The label to apply to the issue or pull request.

## `github.add_reviewer`

**Using YAML actions:** `github.add_reviewer`

**Using Pilot JavaScript:** `$github.addReviewer(...)`

Add a reviewer to a pull request.

If given as a string, the string is interpreted as either a GitHub login or a
GitHub team (in the form `@org/team`) and the target is implicitly determined by
the calling context.

### Inputs

This action expects either a single string argument or an object argument with
the following properties:

- `target` (`string | null`)

  - The target issue or pull request to apply the label to.

    This must be provided unless used where the target is implicitly defined by
    its context.

- `github_login` (`string | null`)

  - The GitHub login (username) to be assigned as a reviewer.

    Exactly one of `github_login` and `github_team` must be specified.

- `github_team` (`string | null`)
  - The GitHub team (in the form `@org/team`) to be assigned as a reviewer.

    Exactly one of `github_login` and `github_team` must be specified.

## `github.compare_commits`

**Using YAML actions:** `github.compare_commits`

**Using Pilot JavaScript:** `$github.compareCommits(...)`

Compare two commits.

### Inputs

This action expects either a single string argument or an object argument with
the following properties:

- `owner` (`string | null`)
- `repo` (`string | null`)
- `base` (`string`)

  - The base ref to compare against. It can be a branch name, tag name, or a
    commit SHA.

- `head` (`string`)
  - The head ref to compare against. It can be a branch name, tag name, or a
    commit SHA.

### Output

Returns an object with the following properties:

- `ahead_by` (`integer`)
- `base_commit`
  (`{comments_url: string, commit: {author: {name: string, email: string, date: string}  | null, comment_count: integer, committer: {name: string, email: string, date: string}  | null, message: string, tree: {sha: string, url: string} , url: string, verification: {verified: boolean, reason: string, signature: string | null, payload: string | null}  | null}  | null} `)
- `behind_by` (`integer`)
- `commits`
  (`Array<{comments_url: string, commit: {author: {name: string, email: string, date: string}  | null, comment_count: integer, committer: {name: string, email: string, date: string}  | null, message: string, tree: {sha: string, url: string} , url: string, verification: {verified: boolean, reason: string, signature: string | null, payload: string | null}  | null}  | null} >`)
- `diff_url` (`string`)
- `files`
  (`Array<{additions: integer, blob_url: string, changes: integer, contents_url: string, deletions: integer, filename: string, patch: string | null, previous_filename: string | null, raw_url: string, sha: string, status: 'added' | 'removed' | 'modified' | 'renamed' | 'copied' | 'unchanged'} >`)
- `merge_base_commit`
  (`{comments_url: string, commit: {author: {name: string, email: string, date: string}  | null, comment_count: integer, committer: {name: string, email: string, date: string}  | null, message: string, tree: {sha: string, url: string} , url: string, verification: {verified: boolean, reason: string, signature: string | null, payload: string | null}  | null}  | null} `)
- `patch_url` (`string`)
- `permalink_url` (`string`)
- `status` (`'diverged' | 'ahead' | 'behind' | 'identical'`)
- `total_commits` (`integer`)
- `url` (`string`)

## `github.remove_label`

**Using YAML actions:** `github.remove_label`

**Using Pilot JavaScript:** `$github.removeLabel(...)`

Remove a label to an issue or pull request.

If given as a string, the string is interpreted as the label to remove and the
target is implicitly determined by the calling context. If the given label is
not present, this action is a no-op.

### Inputs

This action expects either a single string argument or an object argument with
the following properties:

- `target` (`string | null`)

  - The target issue or pull request to remove the label from.

    This must be provided unless used where the target is implicitly defined by
    its context.

- `label` (`string`)
  - The label to remove from the issue or pull request.

## `github.user_matches`

**Using YAML actions:** `github.user_matches`

**Using Pilot JavaScript:** `$github.userMatches(...)`

Determine if a GitHub user matches a given user or team.

If given as a string, the string is interpreted as either a GitHub login or a
GitHub team (in the form `@org/team`).

### Inputs

This action expects either a single string argument or an object argument with
the following properties:

- `target` (`string | null`)

  - The GitHub user to match against.

    This must be provided unless used where the target is implicitly defined by
    its context.

- `github_login` (`string | null`)

  - The GitHub login (username) that the target user must match.

    Exactly one of `github_login` and `github_team` must be specified.

- `github_team` (`string | null`)
  - The GitHub team (in the form `@org/team`) that the target user must be a
    member of.

    Exactly one of `github_login` and `github_team` must be specified.

### Output

Returns `boolean`.
