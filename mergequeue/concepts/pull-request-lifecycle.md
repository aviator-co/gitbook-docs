# Pull Request Lifecycle

MergeQueue tracks your pull request (PR) from the time it is opened until it is
merged (or closed without merging).

## Open

A pull requests is _open_ when it has not been marked as _ready-to-merge_ and is
not closed on GitHub.

Pull requests can be marked as _ready-to-merge_ by adding the
[configured GitHub label](/mergequeue/reference/complete-reference-guide.md#labels),
adding a [slash command](/mergequeue/slash-commands.md) comment to the pull
request, or by using the [Aviator API](/api/README.md).

## Pending

A pull request is _pending_ when it has marked as _ready-to-merge_ and has not
yet passed all the configured queue pre-conditions.

MergeQueue will validate all the necessary pre-conditions (such as passing all
required checks, having no merge conflicts, and having sufficient approvals)
before the pull request enters the queue.

When a pull request is pending, Aviator will report the reason why it has not
yet entered the queue on the
[pull request sticky comment](/mergequeue/concepts/sticky-comments.md).

## Queued

A pull request is _queued_ when it has been marked as _ready-to-merge_ passed
all the necessary queue pre-conditions.

Once queued, MergeQueue will validate the changes in the pull request against
the latest changes in the target branch (depending on the repository's
[queue mode](/mergequeue/concepts/queue-modes.md)) and merge the pull request if
it passes all the required checks.

## Blocked

A pull request is _blocked_ if it could not be merged for some reason.

Possible reasons for a pull request to be blocked include:

- There was a merge conflict with the target branch or another pull request in
  the queue
- One or more of the required checks failed
- Approvals were removed from the pull request
- The HEAD commit of the pull request was modified after queueing

See the
[pull request status code documentation](/mergequeue/reference/comments-and-status-codes.md)
for more details about the possible reasons a pull request can become blocked.

## Merged

A pull request is _merged_ once its changes have been successfully merged into
the pull request's target branch (either by MergeQueue or manually using the
GitHub merge functionality).

Pull requests can sometimes be _merged_ (from MergeQueue's perspective) even if
the GitHub pull request is not literally merged (for example, if the repository
is using
[fast-forwarding](/mergequeue/concepts/parallel-mode/fast-forwarding.md)).

## Closed

A pull request is as _closed_ if it has been closed without merging its changes.