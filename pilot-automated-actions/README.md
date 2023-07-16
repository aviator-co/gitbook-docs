# Pilot - Automated actions

Aviator offers automated actions that can be configured via the configuration file to perform specific actions depending on a particular event or scenario. You can write your own custom scenarios and corresponding actions in the configuration file.

Each Pilot workflow contains one scenario and one or more actions. Here’s an example of a simple scenario:

```yaml
scenarios:
  - name: "instant merge"
    trigger:
      pull_request:
        labeled: "av-instant-merge"
    actions:
      - mergequeue:
          instant_merge: {}
```

In the above example, we are triggering an instant merge action based on the label `av-instant-merge`.

## Scenarios

A scenario is a configurable, automated process that is invoked in response to a certain trigger and which in turn may run several actions. Scenarios are often phrased in English as “if this, then that.”

In the configuration file, the scenarios object has three properties

* `name` - a readable name that uniquely identifies a particular scenario
* `trigger` - represents an object and an associated event that will trigger this scenario
* `actions` - a list of actions that will be triggered in order when the trigger scenario condition is met.

### Triggers

Each trigger is associated with a context of an object. Currently we support the following types of contexts:

#### pull\_request

It has the following associated events:

* **opened**: represents when the PR is opened

```yaml
trigger:
  pull_request: opened
```

* **labeled**: when a GitHub label is added to the PR

```yaml
trigger:
  pull_request:
    labeled: "av-instant-merge"
```

You can also specify who labeled the PR or who the author is. Here a GitHub username can be provided. If you want to instead represent a team, you can use `@org/team`. Exactly one of `github_login` and `github_team` must be specified.

```yaml
trigger:
  pull_request:
    labeled:
      label "av-instant-merge"
      labeled_by: ghusername
```

```yaml
trigger:
  pull_request:
    labeled:
      label "av-instant-merge"
      authored_by: “@aviator-co/engineering”
```

* **synchronize**: The PR was synchronized. This means that the pull request either had new commits pushed to it, was force-pushed, or had its base branch changed.

```yaml
trigger:
  pull_request: synchronize
```

* **submitted**: The PR is ready for review.. This means that it was either opened, or switched from draft to ready for review.

```yaml
trigger:
  pull_request: submitted
```

Note that both submitted and opened events will be triggered when a PR is directly opened in review mode (as non-draft).

#### pull\_request\_review

Associated events:

* **approved**: The PR review provided was of approved type.

```yaml
trigger:
  pull_request_review: approved
```

#### mergequeue

Associated events:

* **queued:** A PR was added to the queue.

```
trigger:
  mergequeue: queued
```

* **dequeued:** A PR was removed from the queue.

```
trigger:
  mergequeue: dequeued
```

* **top\_of\_queue**: A new PR reached the top of the queue.

```
trigger:
  mergequeue: top_of_queue
```

* **merged**: A PR was merged.

```
trigger:
  mergequeue: merged
```

* **blocked**: A PR was blocked by Aviator.

```
trigger:
  mergequeue: blocked
```

* **stuck**: A PR was marked as stuck by Aviator.

```
trigger:
  mergequeue: stuck
```

* **reset**: The queue was reset.

```
trigger:
  mergequeue: reset
```

#### schedule

Used to trigger scheduled events. Please read [scheduled-events.md](scheduled-events.md "mention") for examples.

* **cron\_utc** - required string parameter. Accepts a [<mark style="color:blue;">unix cron format</mark>](https://www.ibm.com/docs/en/db2/11.5?topic=task-unix-cron-format). You can use a cron visual tool like [crontab.guru](https://crontab.guru/) for building your cron string.&#x20;

### Actions

You can specify one or more actions to be executed based on the triggers specified in the scenarios. These actions are performed serially in the order specified and are executed in the context of the trigger. For instance, if we specify the `labeled` event for a `pull_request` in the trigger, then the associated action can be performed on the same PR that was labeled.

There are a few types of actions.

#### github

These include actions to add a label or add a reviewer.

```yaml
actions:
  - github:
    add_label: urgent
  - github:
    add_reviewer: ghusername  # or “@org/team”
```

#### mergequeue

Used to interact with Aviator’s merge queue. The `queue` action will enqueue the PR and `instant_merge` will merge the PR instantly. You can read more about instant merge behavior [<mark style="color:blue;">here</mark>](../mergequeue/priority-merges/#instant-merge).

```
actions:
  - mergequeue: queue
  - mergequeue:
    instant_merge: {}
```

There's also the ability to `pause` and `unpause`. By default, the entire repository (including all base branches) will be paused/unpaused.

```
actions:
  - mergequeue: pause
  - mergequeue: unpause
```

You can also specify a `branch_pattern` or a `paused_message`, both of these fields are optional.

```
actions:
  - mergequeue:
    pause:
      branch_pattern: release-*
      paused_message: "The release branch is paused while we wait on a fix."
  - mergequeue:
    unpause:
      branch_pattern: release-*
```

#### slack

Send a slack notification to the connected Slack account. Note that this requires [Slack integration](https://docs.aviator.co/reference/slack-integration) to be active. You can either send a slack notification to a connected channel or as DM to a connected user.

When sending a DM to a user, you can specify the GitHub username associated with the user. If no github\_users are specified, then the notification is sent directly to the author of the PR.

```yaml
actions:
  - slack:
      channel:
        text: “A new PR has been posted”
  - slack:
      direct:
        text: “A new PR has been posted”
        github_users:
          - ghuser1
          - ghuser2
```

#### script

If you want more custom controls over the actions, you may also choose to write a custom script using Javascript. All the other actions described above can be triggered via script as well. Here’s a quick example:

```yaml
actions:
  - script:
      code: |
        console.log("hello", "world", event, event.target, event.pullRequest.number);
        aviator.github.addLabel({ label: "urgent", target: event.pullRequest.id });
```