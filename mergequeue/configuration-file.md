---
description: >-
  Learn what merge rules are applied in MergeQueue to use them more efficiently.
  Rules affect how the Aviator bot reacts to the actions happening on GitHub.
---

# Merge Rules

MergeQueue communicates with pull request using GitHub labels, GitHub comments and the [<mark style="color:blue;">Aviator CLI</mark>](../aviator-cli/). Merge rules are at the core of how the Aviator bot reacts to the actions taken on GitHub.

Some of the Basic Configuration can be modified using the [<mark style="color:blue;">Merge Rules dashboard</mark>](https://app.aviator.co/github/rules). For more advanced configuration, Aviator supports a YAML based configuration file. This file can either be applied directly from the Aviator dashboard or configured in the GitHub repository.

### Managing YAML from the dashboard

You can directly apply the config on the _YAML configuration_ tab on the [<mark style="color:blue;">Merge Rules</mark>](https://app.aviator.co/github/validate-config) page. We also recommend validating this configuration before applying any changes.

<figure><img src="../.gitbook/assets/Screen Shot 2023-10-12 at 3.22.07 PM.png" alt=""><figcaption></figcaption></figure>

### Managing YAML from GitHub repository

You can also create a configuration file stored in `.aviator/config.yml`. The file will only be read once it is merged into the repository's default branch. It will also override any properties set in the Dashboard UI.

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
