---
description: >-
  Learn the main states a pull request can be in MergeQueue. MergeQueue tracks
  pull requests from the time they are opened until they are merged or closed.
---

# Pull Request Lifecycle

MergeQueue tracks your pull request from the time it is opened until it is merged (or closed without merging). There are several different states a pull request can be in at any given time.

## Open

A pull request is _open_ when it has not been marked as _ready-to-merge_ and is not closed on GitHub.

Pull requests can be marked as _ready-to-merge_ by adding the label configured in `merge_rules.labels` in [<mark style="color:blue;">the YAML configuration</mark>](https://app.aviator.co/schema/index.html#aviator_config_yaml.json), adding a [<mark style="color:blue;">slash command</mark>](../reference/slash-commands.md) comment to the pull request, or using the [<mark style="color:blue;">Aviator API</mark>](../../api/).

## Pending

A pull request is _pending_ when it has been marked as _ready-to-merge_ but is not yet ready to enter the queue for some reason.

MergeQueue will validate all the necessary pre-conditions (such as passing all required checks, having no merge conflicts, and having sufficient approvals) before the pull request enters the queue.

Generally, a pull request is pending if it will enter the queue without any action from the pull request author (for example, the pull request is still running some required tests when the `check_mergeability_to_queue` setting is enabled).

When a pull request is pending, Aviator will report the reason why it has not yet entered the queue on the [<mark style="color:blue;">pull request sticky comment</mark>](sticky-comments.md).

## Queued

A pull request is _queued_ when it has been marked as _ready-to-merge_ and has passed all the necessary queue pre-conditions.

Once queued, MergeQueue will validate the changes in the pull request against the latest changes in the target branch (depending on the repository's [<mark style="color:blue;">queue mode</mark>](queue-modes.md)) and merge the pull request if it passes all the required checks.

## Blocked

A pull request is _blocked_ if it cannot not be merged and requires action from the pull request author (such as resolving merge conflicts or fixing broken tests).

Possible reasons for a pull request to be blocked include:

* There was a merge conflict with the target branch or another pull request in the queue.
* One or more of the required checks failed.
* Approvals were removed from the pull request after it entered the queue.
* The HEAD commit of the pull request was modified after it was marked as _ready-to-merge_.

See the [<mark style="color:blue;">pull request status code documentation</mark>](https://docs.aviator.co/mergequeue/reference/status-codes) for more details about the possible reasons a pull request can become blocked.

## Merged

A pull request is _merged_ once its changes have been successfully merged into the pull request's target branch (either by MergeQueue or manually using the GitHub merge functionality).

Pull requests can sometimes be _merged_ (from MergeQueue's perspective) even if the GitHub pull request is not literally merged (for example, if the repository is using [<mark style="color:blue;">fast-forwarding</mark>](parallel-mode/fast-forwarding.md)).

## Closed

A pull request is _closed_ if it has been closed without merging its changes.
