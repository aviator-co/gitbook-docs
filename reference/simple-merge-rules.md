# Simple merge rules

Merge rules are at the core of how the Aviator bot behaves. MergeQueue primarily relies on GitHub Labels to communicate with pull requests. There are several attributes that you can customize for each repository using a yaml configuration file.

### Merge rules dashboard

The best option for a quick setup is via the [<mark style="color:blue;">Merge Rules</mark>](https://app.aviator.co/github/rules) page. However, the UI does not expose all possible customizations.

{% hint style="warning" %}
Many advanced configurations are not available in the Dashboard UI and can only be configured through a configuration file. We only recommend using the Dashboard UI option if you require a simple setup.
{% endhint %}

The only required setting is the `Label for trigger` -  this will default to `mergequeue`. Once you add this to a PR, Aviator will queue and merge the PR.

![Label for trigger is the only required setting.](<../.gitbook/assets/Screen Shot 2022-05-23 at 5.43.35 PM.png>)

All of the settings in the UI are also covered in the Rules section of [<mark style="color:blue;">Configuration file</mark>](configuration-file.md). Each setting also has a tooltip that provides more information.

#### Configuring Status Checks

See `Required Checks` under `Merge Preconditions` to set status checks for your PRs that need to be validated before merging. Selecting `Use GitHub mergeability check` will use all GitHub required tests for the repo.

<figure><img src="../.gitbook/assets/Screen Shot 2022-12-29 at 9.14.58 AM.png" alt=""><figcaption><p>required check configuration from UI</p></figcaption></figure>

If your team is using Parallel mode, by default, Aviator bot will use the same checks for original PRs and draft PRs. However, if you want to customize checks for draft PRs, you can do so under `Override required checks` in the Parallel Mode section.

![Override required checks for draft PRs.](<../.gitbook/assets/Screen Shot 2022-07-13 at 4.37.18 PM.png>)

<details>

<summary>Complete Yaml Configuration</summary>

```yaml
version: 1.0.0
merge_rules:
  labels:
    trigger: "label_name"
    skip_line: "skip_line"
    merge_failed: "blocked"
    skip_delete_branch: "do-not-delete"
  update_latest: true
  delete_branch: false
  use_rebase: false
  publish_status_check: true
  base_branches:
    - master
    - /release-*/
  enable_comments: true
  ci_timeout_mins: 60
  require_all_checks_pass: false
  preconditions:
    validations:
    - name: missing JIRA ticket in PR title
      match:
        type: title
        regex:
        - -\s\[[^\]]*\]
        - ()
    - name: validation_body
      match:
        type: body
        regex:
        - -\s\[[^\]]*\]
    number_of_approvals: 1
    required_checks:
      - check_1
      - "check 2"
      - name: conditional_check
        acceptable_statuses:
          - skipped
          - success
    use_github_mergeability: true
    conversation_resolution_required: false
  merge_mode:
    type: "parallel"
    parallel_mode:
      use_affected_targets: false
      use_fast_forwarding: false
      max_parallel_builds: 10
      max_requeue_attempts: 3
      stuck_pr_label: "label"
      stuck_pr_timeout_mins: 90
      block_parallel_builds_label: "block_batch"
      check_mergeability_to_queue: false
      override_required_checks:
        - "check 1"
        - check_2
        - name: conditional_check
        acceptable_statuses:
          - cancelled
          - success
      batch_size: 1
      batch_max_wait_minutes: 0
      require_all_draft_checks_pass: false
  auto_update:
    enabled: false
    label: "auto_update"
    max_runs_for_update: 10
  merge_commit:
    use_title_and_body: true
    cut_body_before: "----"
    cut_body_after: "+++"
    apply_title_regexes:
      - pattern: "AVTR-"
        replace: "AVTR_"
      - pattern: "MQ-BOT"
        replace: "MQ BOT"
  merge_strategy:
    name: "squash"
    override_labels:
      squash: "mq-squash"
      rebase: "mq-rebase"
      merge: "mq-merge"
```

</details>
