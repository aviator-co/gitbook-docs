---
icon: robot
description: >-
  Aviator offers automated actions you can configure to perform specific actions
  depending on an event or scenario. Learn how to write custom scenarios here.
---

# Pilot Automated Actions

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

* **ready:** A user requested a PR to be queued. This is triggered at the same time as the `pullrequest.labeled` action if the PR was queued using a GitHub label.&#x20;

```
trigger:
  mergequeue: ready
```

* **queued:** A PR was added to the queue.
  * **skip\_line \[optional]**: A PR marked to skip the line was added to the queue.

```
trigger:
  mergequeue: queued
```

To add the skip\_line option:

```
trigger:
  mergequeue: 
    queued:
      skip_line: true
```

```
trigger:
  mergequeue: dequeued
```

* **added\_to\_batch:** A PR was added to batch for validation. This is applicable only for parallel mode. This will still be called even if the draft PR was skipped due to `skip_draft_when_up_to_date` config setting in the parallel mode.

```
trigger:
  mergequeue: added_to_batch
```

* **removed\_from\_batch:** A PR was removed from the validation batch. This is applicable only for parallel mode. This will still be called even if the draft PR was skipped due to `skip_draft_when_up_to_date` config setting in the parallel mode.

```
trigger:
  mergequeue: removed_from_batch
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

Used to trigger scheduled events. Please read [scheduled-events.md](pilot-automated-actions/scheduled-events.md "mention") for examples.

* **cron\_utc** - required string parameter. Accepts a [<mark style="color:blue;">unix cron format</mark>](https://www.ibm.com/docs/en/db2/11.5?topic=task-unix-cron-format). You can use a cron visual tool like [<mark style="color:blue;">crontab.guru</mark>](https://crontab.guru/) for building your cron string.

### Actions

You can specify one or more actions to be executed based on the triggers specified in the scenarios. These actions are performed serially in the order specified and are executed in the context of the trigger. For instance, if we specify the `labeled` event for a `pull_request` in the trigger, then the associated action can be performed on the same PR that was labeled.

There are a few types of actions.

#### github

These include actions to add a label or add a reviewer.

* **add\_label** - Add a GitHub label to the PR. You can directly pass it the label name.
* **remove\_label** - Remove the provided GitHub label from the PR. If the label doesn't exist, this action will be a no-op.
* **add\_reviewer** - Add a reviewer to the PR. You can directly pass the username or a team name (`@org/team`) as a parameter to this action.

```yaml
actions:
  - github:
    add_label: urgent
  - github:
    remove_label: urgent
  - github:
    add_reviewer: ghusername  # or “@org/team”
```

#### mergequeue

Used to interact with Aviator’s merge queue. The `queue` action will enqueue the PR and `instant_merge` will merge the PR instantly. You can read more about instant merge behavior [<mark style="color:blue;">here</mark>](mergequeue/concepts/priority-merges/#instant-merge).

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

Send a Slack notification to the connected Slack account. Note that this requires [<mark style="color:blue;">Slack integration</mark>](https://docs.aviator.co/reference/slack-integration) to be active.

* **channel**: Send notification to a Slack channel.
  * **text**: The text to send in the notification.
  * **hook\_url \[optional]**: The webhook URL for a Slack channel. If not provided, the default channel set up with the Slack integration will be used.
* **direct**: Send a Slack DM to a user directly.
  * **text**: The text to send in the notification.
  * **blocks \[optional]**: Customized Slack blocks to send in the notification. [<mark style="color:blue;">See Slack's block building kit</mark>](https://api.slack.com/block-kit/building).
  * **github\_users \[optional]**: The GitHub users to notify (must be the GitHub username associated with the user). If not specified, the notification will be sent to the author of the PR.
  * **github\_group \[optional]**: The GitHub group whose members will be notified.
  * **labels \[optional]**: Labels associated with this Slack DM that users can utilize to personalize DMs. By default, all specified users (either `github_users`, `github_group`, or the author of the PR will be notified). If you want to opt-out, see Personal Integrations.
  * **opt-in \[optional]**: If True, then the users will need to voluntarily opt-in to receive the DM.

```yaml
actions:
  - slack:
      channel:
        text: “A new PR has been posted”
        hook_url: “https://hooks.slack.com/services/xxxxxxxxxxxxxxxxx”
  - slack:
      direct:
        text: “A new PR has been posted”
        github_users:
          - login: ghusername1
          - login: ghusername2
        github_group: "eng/infrastructure"
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

The following attributes are accessible via the `event` variable.

* **pullRequest**
  * **url:** The URL of the pull request.
  * **number:** The number of the pull request. (ex. `event.pullRequest.number`)
  * **state:** The state of the pull request. If the trigger is a `pull_request` trigger, options are: `open`, `closed`. If the trigger is a `mergequeue` trigger, options are: `open`, `queued`, `blocked`, `merged`, `tagged`.
  * **labels:** Labels that are on the pull request.
  * **user**
    * **login:** The GitHub login of the user who opened the pull request.
  * **skipLine** (`mergequeue` trigger only)**:** \[boolean] Whether the pull request was queued with skip line.
  * **skipLineReason** (`mergequeue` trigger only)**:** The skip line reason for a pull request if it was given. (ex. `event.pullRequest.skipLineReason`)
* **repository**
  * **name:** The name of the repository. (ex. `event.repository.name`)
  * **full\_name:** The full name of the repository, including the organization name, in the format `org/repo_name`.
