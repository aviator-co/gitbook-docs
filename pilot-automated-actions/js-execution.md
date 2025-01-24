---
description: >-
  Aviator allows for executing custom JS code with Pilot automated actions or
  the MergeQueue ready hook. Learn more details about how it happens in our
  guide.
---

# JavaScript Execution

Aviator has support for executing custom JavaScript code using [Pilot automated actions](../pilot-automated-actions.md) or the [MergeQueue ready hook](../mergequeue/concepts/ready-hook.md).

For security and sandboxing purposes, The Pilot JavaScript execution does not provide most standard web APIs (such as `fetch`). Instead, several integrations with [MergeQueue](reference/mergequeue.md), [GitHub](reference/github.md), and [Slack](reference/slack.md) are provided.

Each Javascript code execution has maximum time limit of **10 seconds**, and you can write arbitrary javascript within the bounds of provided APIs and context.

## Global Context

For each Javascript execution, Aviator provides the a few variables for global context. These variables can be used to query data or take action.

### `$aviator` or `aviator`

Represents the top level context. It has 3 properties: `github`, `slack` and `mergequeue`. These properties can also be used directly with `$github`, `$slack` and `$mergequeue` respectively. Example:

```jsx
aviator.github.addLabel({ label: "urgent", target: event.pullRequest.id });
```

or

```jsx
$aviator.mergequeue.synchronizePullRequest({target: event.pullRequest.id});
```

### `$github`

Corresponds to the [<mark style="color:blue;">GitHub</mark>](reference/github.md) component, and can also be used directly. Example:

```jsx
$github.addLabel({ label: "urgent", target: event.pullRequest.id });
```

### `$slack`

Corresponds to the [<mark style="color:blue;">Slack</mark>](reference/slack.md) component and can also be used directly. Example:

```jsx
$slack.channel({text: "A reset was caused by PR#" + event.pullRequest.number })
```

### `$mergequeue`

Corresponds to the [<mark style="color:blue;">MergeQueue</mark>](reference/mergequeue.md) component, and can be used directly. Example:

```jsx
$mergequeue.copyPullRequest({branch: "c/" + event.pullRequest.head.ref})
```

### `console`

Represents the standard javascript console. Although these console logs are not captured anywhere.

## Local Context

For each Javascript execution, Aviator also provides a local context associated with the trigger that caused the javascript to be executed. This is defined as `event` object. An event can be of multiple types. Each [<mark style="color:blue;">trigger event</mark>](../pilot-automated-actions.md#triggers) represents one even type:

### `PullRequestEvent`

Associated with a `pull_request` trigger. It has a the following properties:

* `event.pullRequest` - a `PullRequest` object
* `event.action` - string representing the action on the pull request.
* `event.sender` - a `GitHubUser` object representing the user who took the action on the pull request
* `event.repository` - a `GitHub` repository object

### `PullRequestReviewEvent`

Associated with a `pull_request_review` trigger. It has the following properties:

* `event.pullRequest` - a `PullRequest` object
* `event.action` - string representing the action on the pull request.
* `event.sender` - a `GitHubUser` object representing the user who took the action on the pull request
* `event.repository` - a `GitHubRepository` object
* `event.review` - a `Review` object

### `MergeQueueEvent`

Associated with a `merge_queue` trigger. It has the following properties:

* `event.pullRequest` - a `PullRequest` object
* `event.action` - string representing the action on the pull request
* `event.repository` - a `GitHubRepository` object

### Event Objects

#### PullRequest

* `url` - string representing the URL of the pull request. E.g. `https://github.com/aviator-co/mergeit/pull/6301`
* `number` - integer representing PR number, e.g.: `6301`
* `state` - string representing state of the PR, possible values: `open`, `pending`, `queued`, `blocked` and `merged`
* `user` - a `GitHubUser` object representing the author of the PR
* `skipLine` - boolean. True when the PR is a skip-line label (associated with MergeQueue)
* `skipLineReason` - string (optional). If skip line reason was provided
* `id` - representing a unique identifier for a pull request. e.g. `aviator-co/mergeit#6301`
* `base` - a `PullRequestRef` object representing the base branch of this PR.
* `head` - a `PullRequestRef` object representing the head branch of this PR.

#### PullRequestRef

* `ref` - string representing a branch, e.g. `master`

#### GitHubUser

* `login` - string representing the GitHub username

#### GitHubRepository

* `name` - string representing the name of the repository, e.g. `mergeit`
* `fullName` - string representing the org name and repository name together, e.g. `aviator-co/mergeit`

#### Review

* `user` - The `GithubUser` who submitted the review
* `body` - string representing the contents of the review
* `state` - string representing `approved, changes_requested, commented`
