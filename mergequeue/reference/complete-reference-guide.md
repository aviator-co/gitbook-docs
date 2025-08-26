---
description: >-
  This page guides you through configuration files and all the possible ways you
  can customize your MergeQueue experience. Check the main Merge Rules you can
  use.
---

# Configuration Reference MergeQueue

MergeQueue communicates with pull request using GitHub labels, GitHub comments and the [<mark style="color:blue;">Aviator CLI</mark>](../../aviator-cli/). To learn about how to apply the rules, read the [<mark style="color:blue;">intro guide to Merge Rules</mark>](../configuration-file.md).

This page will guide you through the entire configuration files and all the possible ways you can customize your MergeQueue experience.

## Merge Rules

### Labels

```yaml
merge_rules:  
  labels:    
    trigger: "label_name"    
    skip_line: "skip_line"    
    merge_failed: "blocked"    
    skip_delete_branch: "do-not-delete"
```

{% tabs %}
{% tab title="Attributes" %}
<table><thead><tr><th width="212.39999999999998">Name</th><th width="150">Type</th><th>Description</th></tr></thead><tbody><tr><td><strong>trigger</strong></td><td>String</td><td>(<em>Required</em>). This label is used to identify that a pull request is ready to be processed by Aviator bot. Once labeled, the bot will verify that the PR has passed all the required conditions and then merge the PR.</td></tr><tr><td><strong>skip_line</strong></td><td>String</td><td>When tagged with this label, Aviator bot will move the PR to the front of the queue.</td></tr><tr><td><strong>merge_failed</strong></td><td>String</td><td>If the pull request fails to merge, Aviator bot will add this label.</td></tr><tr><td><strong>skip_delete_branch</strong></td><td>String</td><td>If <code>delete_branch</code> is enabled, Aviator bot will skip deleting a branch with this label after merging.</td></tr></tbody></table>
{% endtab %}
{% endtabs %}

### Preconditions

```yaml
merge_rules:  
  labels:    
    trigger: "label_name"  
  preconditions:    
    number_of_approvals: 1    
    required_checks:
      # Note: both a string or a more detailed conditional check is accepted
      # for conditional_check, both skipped and success are considered passing      
      - check_1
      - name: conditional_check
        acceptable_statuses:
          - skipped
          - success
      - "golang-*"  # matches golang-test, golang-lint, etc.
    use_github_mergeability: true    
    conversation_resolution_required: false
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
        - -\s\[[^\]]*\]yam
```

{% tabs %}
{% tab title="Attributes" %}
<table><thead><tr><th>Name</th><th width="150">Type</th><th>Description</th></tr></thead><tbody><tr><td><strong>number_of_approvals</strong></td><td>Integer</td><td>Minimum number of reviewers that should approve the PullRequest before it can qualify to be merged. Defaults to 1.</td></tr><tr><td><strong>required_checks</strong></td><td>List[Union[String, ConditionalCheck]]</td><td>Checks that need to pass before Aviator bot will merge the PR. Supports shell wildcard (glob) patterns. Also supports other acceptable CI statuses if your repo uses conditional status checks. See the Example tab.</td></tr><tr><td><strong>use_github_mergeability</strong></td><td>Boolean</td><td>Determines whether to use the default required checks specified in branch protection rules on GitHub. When this setting is enabled, Aviator bot will ignore <code>required_checks</code>. Defaults to <code>true</code>.</td></tr><tr><td><strong>conversation_resolution_required</strong></td><td>Boolean</td><td>Determines whether Aviator bot will queue the PR only after all conversations are resolved. Defaults to <code>false</code>.</td></tr><tr><td><strong>validations</strong></td><td>List[Validation]</td><td>Custom validation rules using regexes for the PR body or title. See the Example tab for more details.</td></tr></tbody></table>
{% endtab %}
{% endtabs %}

#### acceptable\_statuses

By default a required status check has to be in `success` or `skipped` state for the check to be considered valid. When `acceptable_statuses` are provided for a check, Aviator will modify that behavior. The possible values of the status are:

* `success`
* `pending`
* `failure`
* `cancelled`
* `timed_out`
* `skipped`
* `neutral`
* `action_required`
* `stale`
* `unknown`
* `error`
* `blocked`
* `missing`

Note that `missing` is a special status that represents that the status check has not been reported at all in GitHub by the CI provider.

### Queue Modes

<table><thead><tr><th width="150">Name</th><th width="150">Type</th><th>Description</th></tr></thead><tbody><tr><td><strong>type</strong></td><td>String</td><td>Determines the mode. Options are: default, parallel, no-queue.</td></tr></tbody></table>

#### Parallel Mode

The following are only applicable if the above `merge_mode` is set to `parallel`. You can learn more about [<mark style="color:blue;">Parallel Mode here</mark>](../concepts/parallel-mode/).

```
merge_rules:  
  labels:    
    trigger: "label_name"  
  merge_mode:    
    type: "parallel" # other modes: "default", "no-queue"    
    parallel_mode:      
      use_affected_targets: false
      use_fast_forwarding: true      
      max_parallel_builds: 10
      max_parallel_paused_builds: 1      
      max_requeue_attempts: 3
      update_before_requeue: true
      stuck_pr_label: "label"      
      stuck_pr_timeout_mins: 90
      bypass_stuck_state: false
      block_parallel_builds_label: "block_batch"      
      check_mergeability_to_queue: false
      override_required_checks:
        - "check 1"
        - check_2
        # Note: both a string or a more detailed conditional check is accepted
        # for conditional_check, both skipped and success are considered passing      
        - name: conditional_check
          acceptable_statuses:
            - skipped
            - success
      batch_size: 1
      batch_max_wait_minutes: 0
      require_all_draft_checks_pass: false
      skip_draft_when_up_to_date: false
      use_optimistic_validation: true
      optimistic_validation_failure_depth: 2
```

{% tabs %}
{% tab title="Attributes" %}
<table><thead><tr><th>Name</th><th width="150">Type</th><th>Description</th></tr></thead><tbody><tr><td><strong>max_parallel_builds</strong></td><td>Integer</td><td>The maximum number of builds that Aviator bot will run at any time. Defaults to no limit.</td></tr><tr><td><strong>max_parallel_paused_builds</strong></td><td>Integer</td><td>Must be less than <code>max_parallel_builds</code>. Defaults to <code>null</code>.<br><br>The maximum number of PRs in a paused state that Aviator will create draft PRs for. If set to 0, Aviator will not create any draft PRs on paused base branches. If set to <code>null</code> there will be no specific limit for paused PRs.<br><br>The number of paused draft PRs always counts towards the cap set by <code>max_parallel_builds</code>.</td></tr><tr><td><strong>max_requeue_attempts</strong></td><td>Integer</td><td>The maximum number of times Aviator bot will requeue a CI run after failing. Note that PRs will only be requeued if the original PR CI is passing but the draft PR CI fails. Defaults to no requeuing.</td></tr><tr><td><strong>update_before_requeue</strong></td><td>Boolean</td><td>Whether to update the PR with the base branch when doing an auto-requeue. This is only applicable if <code>max_requeue_attempts</code> is set. Defaults to <code>false</code>.</td></tr><tr><td><strong>stuck_pr_label</strong></td><td>String</td><td>The label that Aviator bot will add if it determines a PR to be stuck.</td></tr><tr><td><strong>stuck_pr_timeout_mins</strong></td><td>Integer</td><td>Aviator bot will determine the PR to be stuck after the specified timeout and dequeue it. A stuck state in parallel mode is when the draft PR has passed CI but the original PR's CI is still pending. Defaults to 0, which means that Aviator will dequeue the PR immediately.</td></tr><tr><td><strong>bypass_stuck_state</strong></td><td>Boolean</td><td>If enabled, Aviator will try to bypass the stuck state and try to merge the PR in the stuck state itself. Defaults to <code>false</code></td></tr><tr><td><strong>block_parallel_builds_label</strong></td><td>String</td><td>Once added to a PR, no further Draft PRs will be built on top of it until that PR is merged or dequeued.</td></tr><tr><td><strong>check_mergeability_to_queue</strong></td><td>Boolean</td><td>If enabled, Aviator bot will only queue the PR if it passes all mergeability checks. Defaults to <code>false</code>.</td></tr><tr><td><strong>use_affected_targets</strong></td><td>Boolean</td><td>If enabled, allows using <a href="../concepts/affected-targets/">affected targets</a> in your repo.</td></tr><tr><td><strong>use_fast_forwarding</strong></td><td>Boolean</td><td>If enabled, uses <a href="https://docs.aviator.co/mergequeue/concepts/fast-forwarding">fast forwarding</a> to merge PRs.</td></tr><tr><td><strong>override_required_checks</strong></td><td>List[Union[String, ConditionalCheck]]</td><td>Use this attribute if you would like different checks for your original PRs and draft PRs. The checks defined here will be used for the draft PRs created in Parallel mode. Supports shell wildcard (glob) patterns. Also supports other acceptable CI statuses if your repo uses conditional status checks. See the Example tab.</td></tr><tr><td><strong>batch_size</strong></td><td>Integer</td><td>The number of queued PRs batched together for a draft PR CI run. Defaults to 1.</td></tr><tr><td><strong>batch_max_wait_minutes</strong></td><td>Integer</td><td>The time to wait before creating the next batch of PRs if there are not enough queued PRs to create a full batch. Defaults to 0.</td></tr><tr><td><strong>require_all_draft_checks_pass</strong></td><td>Boolean</td><td>Determines if Aviator will enforce all checks to pass for the constructed draft PRs. If true, any single failing test will cause the draft PR to fail. These checks include the ones we receive status updates for via GitHub. This may work well if your repo has conditional checks. Requires at least one check to be present. Defaults to <code>false</code>.</td></tr><tr><td><strong>skip_draft_when_up_to_date</strong></td><td>Boolean</td><td>Skips creation of the staging draft PR to validate the CI if the original PR is already up to date and no other PR is currently queued. This is usually a good optimization to avoid running extra CI cycles. Defaults to <code>true</code>.</td></tr><tr><td><strong>use_optimistic_validation</strong></td><td>Boolean</td><td>If the CI of the top staging draft PR is still running but a subsequent draft PR passes, then optimistically use that success result to validate the top PR as passing. Defaults to <code>true</code>.</td></tr><tr><td><strong>optimistic_validation_failure_depth</strong></td><td>Integer</td><td>Requires <code>use_optimistic_validation</code> to be <code>true</code>. If the CI of the top staging draft PR has failed, wait for subsequent draft PR CIs to also fail before dequeuing the PR. The number represents how many draft PRs do we wait to fail before dequeuing a PR. For e.g., if set to 1, it will dequeue immediately after the top staging draft PR fails, but if set to 2, it will wait for one more subsequent draft PR to fail before dequeuing the top PR. Value should always be <code>1</code> or larger. Defaults to <code>1</code>.</td></tr></tbody></table>
{% endtab %}
{% endtabs %}

### Auto Update

```
merge_rules:  
  labels:    
    trigger: "label_name"  
  auto_update:    
    enabled: true    
    label: "auto_update"    
    max_runs_for_update: 10
```

{% tabs %}
{% tab title="Attributes" %}
<table><thead><tr><th width="237.94339622641508">Name</th><th width="150">Type</th><th>Description</th></tr></thead><tbody><tr><td><strong>enabled</strong></td><td>Boolean</td><td>If enabled, Aviator bot will keep your branches up to date with the main branch. Defaults to <code>false</code>.</td></tr><tr><td><strong>label</strong></td><td>String</td><td>Aviator bot will only keep branches with this label up to date with the main branch. Leave empty to auto merge without a label. If no label is provided and <code>auto_update</code> is enabled, by default the Aviator bot will update all PRs.</td></tr><tr><td><strong>max_runs_for_update</strong></td><td>Integer</td><td>The maximum number of times Aviator bot will update your branch. Defaults to no limit.</td></tr></tbody></table>
{% endtab %}
{% endtabs %}

### Merge Commit

```
merge_rules:
  labels:
    trigger: "label_name"
  merge_commit:
    use_title_and_body: true
    cut_body_before: "----"
    cut_body_after: "+++"
    strip_html_comments: false
    include_coauthors: true
    apply_title_regexes:
      - pattern: "AVTR-"
        replace: "AVTR_"
      - pattern: "MQ-BOT"
        replace: "MQ BOT"
```

{% tabs %}
{% tab title="Attributes" %}
<table><thead><tr><th width="226.5463019353641">Name</th><th width="150">Type</th><th>Description</th></tr></thead><tbody><tr><td><strong>use_title_and_body</strong></td><td>Boolean</td><td>Determines whether Aviator bot will replace default commit messages offered by GitHub. Defaults to <code>false</code>.</td></tr><tr><td><strong>cut_body_before</strong></td><td>String</td><td>A marker string to cut the PR body description. The commit message will contain the PR body after this marker. Leave empty for no cropping.</td></tr><tr><td><strong>cut_body_after</strong></td><td>String</td><td>A marker string to cut the PR body description. The commit message will contain the PR body before this marker. Leave empty for no cropping.</td></tr><tr><td><strong>strip_html_comments</strong></td><td>Boolean</td><td>Strip out the hidden HTML comments from the commit message when merging the PR. Defaults to <code>false</code>.</td></tr><tr><td><strong>include_coauthors</strong></td><td>Boolean</td><td>Include coauthors (if any) in the commit message when merging the PR. Defaults to <code>true</code>.</td></tr><tr><td><strong>apply_title_regexes</strong></td><td>List[ReplacePattern]</td><td>Contains the strings <code>pattern</code> and <code>replace</code> to indicate what pattern to replace in the PR title. This will be applied to the merge commit. In parallel mode, this also applies to the draft PR title. See the example tab for more details.</td></tr></tbody></table>
{% endtab %}
{% endtabs %}

### Merge Strategy

| Name                                          | Type    | Description                                                                                                                                                                                                                                                                          |
| --------------------------------------------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **name**                                      | String  | Defines the merge strategy to use, the options are "squash", "merge", and "rebase". See the [GitHub docs](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/configuring-pull-request-merges/about-merge-methods-on-github) for more info.   |
| **use\_separate\_commits\_for\_stack**        | Boolean | <p>If enabled, uses independent commits for stacked PRs. This requires setting up</p><p>Rulesets in GitHub and allow Aviator to bypass branch protection rules. Otherwise GitHub blocks commits from merging without approval and CI completion. Defaults to <code>false</code>.</p> |
| **update\_pr\_commits\_before\_stack\_merge** | Boolean | If enabled, force updates the commits in the branch of each stacked PR right before merging the PR, so that the PR can be marked as merged instead of closed. This requires  `use_separate_commits_for_stack` config to be enabled. Defaults to `false`.                             |

#### Override Labels

You can specify overrides to the above merge strategy using GitHub Labels. When a PR is flagged with these labels along with the trigger label, Aviator bot will merge the PR using this strategy instead of the default strategy. Leave empty for no overrides.

```
merge_rules:
  labels:
    trigger: "label_name"
  merge_strategy:
    name: "squash"
    override_labels:
      squash: "mq-squash"
      merge: "mq-merge-commit"
      rebase: "mq-rebase"
    use_separate_commits_for_stack: true
    update_pr_commits_before_stack_merge: true
```

{% tabs %}
{% tab title="Attributes" %}
<table><thead><tr><th width="150">Name</th><th width="150">Type</th><th>Description</th></tr></thead><tbody><tr><td><strong>squash</strong></td><td>String</td><td>If marked with this label, the PR will be squashed and merged.</td></tr><tr><td><strong>merge</strong></td><td>String</td><td>If marked with this label, the PR will be merged using a merge commit.</td></tr><tr><td><strong>rebase</strong></td><td>String</td><td>If marked with this label, the PR will be rebased and merged.</td></tr></tbody></table>
{% endtab %}
{% endtabs %}

### Status Comment

Aviator posts a status comment on every open pull request by default. Aviator automatically updates the status comment whenever the pull request is updated.

Custom messages can be specified (`open_message`, `queued_message`, and `blocked_message`) to add information specific to your organization or repository (such as common troubleshooting steps).

```yaml
merge_rules:
  # Configuration for the Aviator status comment.
  # Optional (defaults to publish: "always" with no custom messages).
  status_comment:
    # When to publish the Aviator status comment.
    # Optional. Valid values are "always", "queued", or "never".
    # Default value is "always".
    #   - "always": Post the status comment whenever the pull request is opened.
    #   - "ready": Publish the status comment when the pull request is ready for review.
    #   - "queued": Post the status comment when the pull request is queued.
    #   - "never": Disable the status comment.
    publish: "always"
    
    # A message to include in the status comment if the pull request is in the
    # open state. Supports markdown.
    # Optional.
    open_message: "..."
    
    # A message to include in the status comment if the pull request is in the
    # queued state. Supports markdown.
    # Optional.
    queued_message: "..."
    
    # A message to include in the status comment if the pull request is in the
    # blocked state. Supports markdown.
    # Optional.
    blocked_message: "..."
```

{% tabs %}
{% tab title="Attributes" %}
| Name                 | Type   | Description                                                                                              |
| -------------------- | ------ | -------------------------------------------------------------------------------------------------------- |
| **publish**          | String | One of `always`, `ready`, `queued`, or `never`. Defaults to `always`.                                    |
| **open\_message**    | String | An optional message to include in the Aviator status comment when the pull request is open (not queued). |
| **queued\_message**  | String | An optional message to include in the Aviator status comment when the pull request is queued.            |
| **blocked\_message** | String | An optional message to include in the Aviator status comment when the pull request is blocked.           |
{% endtab %}
{% endtabs %}

### Other

```
merge_rules:
  labels:
    trigger: "label_name"
  update_latest: true
  delete_branch: false
  use_rebase: false
  publish_status_check: "ready"
  enable_comments: true
  ci_timeout_mins: 60
  base_branches:
    - master
    - /release-*/
```

{% tabs %}
{% tab title="Attributes" %}
<table><thead><tr><th width="251.8637200736648">Name</th><th width="132">Type</th><th>Description</th></tr></thead><tbody><tr><td><strong>update_latest</strong></td><td>Boolean</td><td>Determines whether Aviator bot will merge the latest base branch into the current branch of your PR before verifying the CI statuses. This is only compatible with the <code>default</code> and <code>no-queue</code> merge modes. Defaults to <code>true</code>.</td></tr><tr><td><strong>delete_branch</strong></td><td>Boolean</td><td>Determines whether Aviator bot will delete the branch after merging. Defaults to <code>false</code>.</td></tr><tr><td><strong>use_rebase</strong></td><td>Boolean</td><td>Determines if Aviator bot will use rebase to update PRs. This feature is only available in the Pro plan. Please note that you also need to enable <code>update_latest</code> for rebase to work. Defaults to <code>false</code>.</td></tr><tr><td><strong>publish_status_check</strong></td><td>Boolean or String</td><td>Determines if Aviator bot will publish a status check back on the PR. This status check represents the current state of PR in the Aviator queue.<br><br>Possible values:<br><code>always</code>: Post the status check whenever the pull request is opened.<br><code>ready</code>: Publish the status check when the pull request is ready for review.<br><code>queued</code>: Post the status check when the pull request is queued.<br><code>never</code>: Disable the status check.<br><br>For backward compatibility this value also supports boolean.<br><code>true</code> same as <code>ready</code><br><code>false</code> same as <code>never</code><br><br>Defaults to <code>ready</code></td></tr><tr><td><strong>enable_comments</strong></td><td>Boolean</td><td>Determines if Aviator bot can add comments on the PR to describe the actions performed on the PR by the bot. Aviator bot comments include information such as failure reasons and the position of the PR in the queue. Defaults to <code>true</code>.</td></tr><tr><td><strong>ci_timeout_mins</strong></td><td>Integer</td><td>The time before we determine that the CI has timed out. Defaults to 0 that is equivalent to no time out.</td></tr><tr><td><strong>base_branches</strong></td><td>List[String]</td><td>These branches are the ones that Aviator will monitor as valid branches to merge into. Defaults to your repository default branch as configured on GitHub. Regexes are allowed.</td></tr><tr><td><strong>require_all_checks_pass</strong></td><td>Boolean</td><td>Determines if Aviator will enforce all checks to pass. If true, any single failing test will cause the PR to fail. These checks include the ones we receive status updates for via GitHub. This may work well if your repo has conditional checks. Requires at least one check to be present. Defaults to <code>false</code>.</td></tr><tr><td><strong>require_skip_line_reason</strong></td><td>Boolean</td><td>If enabled, a reason is required when marking a PR as skip line. Defaults to false.<br>Provide a reason via Aviator slash command as a GitHub comment:<br> <code>/aviator merge --skip-line=&#x3C;insert reason for skipping></code></td></tr><tr><td><strong>auto_detect_stacks</strong></td><td>Boolean</td><td>If enabled, Aviator will automatically detect stacked PRs, this could be  useful if you are using a thirdparty tool to stack PRs. This is not needed when using Aviator CLI. Defaults to <code>false</code>.</td></tr></tbody></table>


{% endtab %}
{% endtabs %}
