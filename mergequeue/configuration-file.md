---
description: >-
  Learn what merge rules are applied in MergeQueue to use them more efficiently.
  Rules affect how the Aviator bot reacts to the actions happening on GitHub.
---

# Merge Rules

MergeQueue communicates with pull request using GitHub labels, GitHub comments and the [<mark style="color:blue;">Aviator CLI</mark>](../aviator-cli/). Merge rules are at the core of how the Aviator bot reacts to the actions taken on GitHub.

Some of the Basic Configuration can be modified using the [<mark style="color:blue;">Merge Rules dashboard</mark>](https://app.aviator.co/queue/config). For more advanced configuration, Aviator supports a YAML based configuration file. This file can either be applied directly from the Aviator dashboard or configured in the GitHub repository.

### Managing YAML from the dashboard

You can edit the YAML config directly on the [<mark style="color:blue;">Merge Rules</mark>](https://app.aviator.co/queue/config) page. Use the **Validate** button to check the config, then click **Save & Apply** to apply your changes. We recommend validating the configuration before saving.

<figure><img src="../.gitbook/assets/Screen Shot 2023-10-12 at 3.22.07 PM.png" alt=""><figcaption></figcaption></figure>

### Managing YAML from GitHub repository

You can also create a configuration file stored in `.aviator/config.yml`. The file will only be read once it is merged into the repository's default branch. It will also override any properties set in the Dashboard UI.

To validate your configuration before merging, use the `/aviator validate-config` [slash command](reference/slash-commands.md#validate-config) on your pull request. Aviator will post a comment indicating whether the config is valid or listing any errors.

### Config Schema

You can see [<mark style="color:blue;">the complete config schema</mark>](https://app.aviator.co/schema/index.html#aviator_config_yaml.json) as well as [<mark style="color:blue;">the JSON schema</mark>](https://app.aviator.co/schema/aviator_config_yaml.json) for autocompletion and validation purpose.

## Examples

Find below some common examples to get you started.

### Minimalist config

The only required attribute is `merge_rules.labels.trigger`.

```yaml
merge_rules:
  labels:
    trigger: "mergequeue"
```

### Custom required checks

Checkout customizing required checks section for details.

```yaml
merge_rules:
  labels:
    trigger: "mergequeue"
  preconditions:
    use_github_mergeability: false
    required_checks:
      - build
      - unit-test
      - typecheck
      - ci/circleci: pytest

```

### Require all conversation resolution

This enforces all conversations in GitHub to be resolved before the PR can be merged.

```yaml
merge_rules:
  labels:
    trigger: "mergequeue"
  preconditions:
    conversation_resolution_required: true
```

### No approval

Useful for testing. Aviator will only be able to merge PR if the approval is not enforced at GitHub level. By default, Aviator always require approvals.

```yaml
merge_rules:
  labels:
    trigger: "mergequeue"
  preconditions:
    number_of_approvals: 0
```

### Using Parallel mode

Also checkout the [<mark style="color:blue;">parallel mode section</mark>](concepts/parallel-mode/) for details.

```yaml
 merge_rules:
   labels:
     trigger: "mergequeue"
   merge_mode:
    type: "parallel"
    parallel_mode:
      max_parallel_builds: 10
      override_required_checks:
        - build_and_test
        - 'ci/circleci: build'
```

#### Managing parallelism

Since every draft PR triggers a new CI run, you typically want to cap how many run at once. These properties in `parallel_mode` control how much work Aviator keeps in flight:

* **`max_parallel_builds`** — The maximum number of builds Aviator runs at any time. Once this limit is reached, the bot stops creating new draft PRs until one of the in-flight draft PRs is closed. Defaults to no limit.
* **`max_topup_builds`** — The number of extra draft PRs that may be tagged on top of `max_parallel_builds` once their builds have passed CI but are waiting to merge behind earlier PRs. When `0` (default), `max_parallel_builds` caps the total number of in-flight draft PRs, whether passed or still running. When set, `max_parallel_builds` instead caps only the draft PRs actively running CI, and Aviator tops up to keep that many builds running while allowing up to `max_parallel_builds + max_topup_builds` draft PRs open in total. This keeps your CI capacity busy instead of leaving slots idle while passed PRs wait to merge. Requires `max_parallel_builds` to be set.
* **`max_parallel_paused_builds`** — Must be less than `max_parallel_builds`. The maximum number of PRs in a [paused](concepts/paused-queues.md) state that Aviator will create draft PRs for. If set to `0`, Aviator will not create any draft PRs on paused base branches. If `null` (default), there is no specific limit for paused PRs. Paused draft PRs always count toward the `max_parallel_builds` cap.
* **`block_parallel_builds_label`** — A GitHub label that pauses queuing until a particular PR is merged. Once added to a PR, no further draft PRs are built on top of it until that PR is merged or dequeued. Useful when a PR touches files that trigger long-running CI that every subsequent draft PR would otherwise inherit.

```yaml
 merge_rules:
   labels:
     trigger: "mergequeue"
   merge_mode:
    type: "parallel"
    parallel_mode:
      max_parallel_builds: 10
      max_topup_builds: 5
      max_parallel_paused_builds: 1
      block_parallel_builds_label: "blocked"
```

### Automatic requeue

On failure, the PRs will automatically requeue before giving up. Only available in parallel mode.

```yaml
 merge_rules:
   labels:
     trigger: "mergequeue"
   merge_mode:
    type: "parallel"
    parallel_mode:
      max_parallel_builds: 10
      max_requeue_attempts: 3
```

### Auto update

Keep your PRs up to date. Every time a new commit is added to the base branch, the PRs are automatically updated using rebase or merge commit. See `merge_rules.auto_update` in [<mark style="color:blue;">the configuration schema reference</mark>](https://app.aviator.co/schema/index.html#aviator_config_yaml.json).

```yaml
 merge_rules:
   labels:
     trigger: "mergequeue"
   auto_update:
     enabled: false
     label: "auto_update"
     max_runs_for_update: 10
```

### Custom title and body

Customize title and body when merging the PR.

```
 merge_rules:
   labels:
     trigger: "mergequeue"
   merge_commit:
     use_title_and_body: true
     cut_body_before: "----"
     cut_body_after: "+++"
```
