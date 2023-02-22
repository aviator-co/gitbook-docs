# Configuration file

## Applying the config

If you are looking for more customization options, you may want to use the YAML based configuration file. There are two ways to set up a configuration file.

### From dashboard

You can directly apply the config on the `Yaml configuration` tab on the [<mark style="color:blue;">Merge Rules</mark>](https://app.aviator.co/github/rules) page. Here you can also validate this configuration before applying.

<figure><img src="../.gitbook/assets/Screen Shot 2022-05-23 at 5.38.57 PM (1).png" alt=""><figcaption><p>applying YAML configuration from dashboard</p></figcaption></figure>

### From GitHub repository

You can also create a configuration file stored in `.mergequeue/config.yml`. The file will only be read once it is merged into the repository's default branch. It will also override any properties set in the Dashboard UI. The only required attribute is `merge_rules`.`labels.trigger.`

## Rules

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
| Name                     | Type   | Description                                                                                                                                                                                                           |
| ------------------------ | ------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **trigger**              | String | (_Required_). This label is used to identify that a pull request is ready to be processed by Aviator bot. Once labeled, the bot will verify that the PR has passed all the required conditions and then merge the PR. |
| **skip\_line**           | String | When tagged with this label, Aviator bot will move the PR to the front of the queue.                                                                                                                                  |
| **merge\_failed**        | String | If the pull request fails to merge, Aviator bot will add this label.                                                                                                                                                  |
| **skip\_delete\_branch** | String | If `delete_branch` is enabled, Aviator bot will skip deleting a branch with this label after merging.                                                                                                                 |
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
| Name                                                                    | Type                                    | Description                                                                                                                                                                                        |
| ----------------------------------------------------------------------- | --------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **number\_of\_approvals**                                               | Integer                                 | Minimum number of reviewers that should approve the PullRequest before it can qualify to be merged. Defaults to 1.                                                                                 |
| **required\_checks**                                                    | List\[Union\[String, ConditionalCheck]] | Checks that need to pass before Aviator bot will merge the PR. Also supports other acceptable CI statuses if your repo uses conditional status checks. See the Example tab.                        |
| <p><strong></strong></p><p><strong>use_github_mergeability</strong></p> | Boolean                                 | Determines whether to use the default required checks specified in branch protection rules on GitHub. When this setting is enabled, Aviator bot will ignore `required_checks`. Defaults to `true`. |
| **conversation\_resolution\_required**                                  | Boolean                                 | Determines whether Aviator bot will queue the PR only after all conversations are resolved.                                                                                                        |
| **validations**                                                         | List\[Validation]                       | Custom validation rules using regexes for the PR body or title. See the Example tab for more details.                                                                                              |
{% endtab %}
{% endtabs %}

### Merge Modes

| Name     | Type   | Description                                                    |
| -------- | ------ | -------------------------------------------------------------- |
| **type** | String | Determines the mode. Options are: default, parallel, no-queue. |

#### Parallel Mode

The following are only applicable if the above `merge_mode` is set to `parallel`. You can learn more about [<mark style="color:blue;">Parallel Mode here</mark>](../how-to-guides/parallel-mode/).

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
      max_requeue_attempts: 3      
      stuck_pr_label: "label"      
      stuck_pr_timeout_mins: 90      
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
| Name                                       | Type                                    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| ------------------------------------------ | --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **max\_parallel\_builds**                  | Integer                                 | The maximum number of builds that Aviator bot will run at any time. Defaults to no limit.                                                                                                                                                                                                                                                                                                                                                                                                                          |
| **max\_requeue\_attempts**                 | Integer                                 | The maximum number of times Aviator bot will requeue a CI run after failing. Note that PRs will only be requeued if the original PR CI is passing but the draft PR CI fails. Defaults to no requeuing.                                                                                                                                                                                                                                                                                                             |
| **stuck\_pr\_label**                       | String                                  | The label that Aviator bot will add if it determines a PR to be stuck.                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| **stuck\_pr\_timeout\_mins**               | Integer                                 | Aviator bot will determine the PR to be stuck after the specified timeout and dequeue it. Defaults to no time out.                                                                                                                                                                                                                                                                                                                                                                                                 |
| **block\_parallel\_builds\_label**         | String                                  | Once added to a PR, no further Draft PRs will be built on top of it until that PR is merged or dequeued.                                                                                                                                                                                                                                                                                                                                                                                                           |
| **check\_mergeability\_to\_queue**         | Boolean                                 | If enabled, Aviator bot will only queue the PR if it passes all mergeability checks. Defaults to `false`.                                                                                                                                                                                                                                                                                                                                                                                                          |
| **use\_affected\_targets**                 | Boolean                                 | If enabled, allows using [affected targets](../how-to-guides/affected-targets/) in your repo.                                                                                                                                                                                                                                                                                                                                                                                                                      |
| **use\_fast\_forwarding**                  | Boolean                                 | If enabled, uses [fast forwarding](../how-to-guides/fast-forwarding.md) to merge PRs.                                                                                                                                                                                                                                                                                                                                                                                                                              |
| **override\_required\_checks**             | List\[Union\[String, ConditionalCheck]] | Use this attribute if you would like different checks for your original PRs and draft PRs. The checks defined here will be used for the draft PRs created in Parallel mode. Also supports other acceptable CI statuses if your repo uses conditional status checks. See the Example tab.                                                                                                                                                                                                                           |
| **batch\_size**                            | Integer                                 | The number of queued PRs batched together for a draft PR CI run. Defaults to 1.                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| **batch\_max\_wait\_minutes**              | Integer                                 | The time to wait before creating the next batch of PRs if there are not enough queued PRs to create a full batch. Defaults to 0.                                                                                                                                                                                                                                                                                                                                                                                   |
| **require\_all\_draft\_checks\_pass**      | Boolean                                 | Determines if Aviator will enforce all checks to pass for the constructed draft PRs. If true, any single failing test will cause the draft PR to fail. These checks include the ones we receive status updates for via GitHub. This may work well if your repo has conditional checks. Requires at least one check to be present. Defaults to `false`.                                                                                                                                                             |
| **skip\_draft\_when\_up\_to\_date**        | Boolean                                 | Skips creation of the staging draft PR to validate the CI if the original PR is already up to date and no other PR is currently queued. This is usually a good optimization to avoid running extra CI cycles. Defaults to `true`.                                                                                                                                                                                                                                                                                  |
| **use\_optimistic\_validation**            | Boolean                                 | If the CI of the top staging draft PR is still running but a subsequent draft PR passes, then optimistically use that success result to validate the top PR as passing. Defaults to `true`.                                                                                                                                                                                                                                                                                                                        |
| **optimistic\_validation\_failure\_depth** | Integer                                 | Requires `use_optimistic_validation` to be `true`. If the CI of the top staging draft PR has failed, wait for subsequent draft PR CIs to also fail before dequeuing the PR. The number represents how many draft PRs do we wait to fail before dequeuing a PR. For e.g., if set to 1, it will dequeue immediately after the top staging draft PR fails, but if set to 2, it will wait for one more subsequent draft PR to fail before dequeuing the top PR. Value should always be `1` or larger. Defaults to `1`. |


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
| Name                       | Type    | Description                                                                                                                                                                                                                           |
| -------------------------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **enabled**                | Boolean | If enabled, Aviator bot will keep your branches up to date with the main branch. Defaults to `false`.                                                                                                                                 |
| **label**                  | String  | Aviator bot will only keep branches with this label up to date with the main branch. Leave empty to auto merge without a label. If no label is provided and `auto_update` is enabled, by default the Aviator bot will update all PRs. |
| **max\_runs\_for\_update** | Integer | The maximum number of times Aviator bot will update your branch. Defaults to no limit.                                                                                                                                                |
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
| Name                      | Type                  | Description                                                                                                                                                                                                                                  |
| ------------------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **use\_title\_and\_body** | Boolean               | Determines whether Aviator bot will replace default commit messages offered by GitHub. Defaults to `false`.                                                                                                                                  |
| **cut\_body\_before**     | String                | A marker string to cut the PR body description. The commit message will contain the PR body after this marker. Leave empty for no cropping.                                                                                                  |
| **cut\_body\_after**      | String                | A marker string to cut the PR body description. The commit message will contain the PR body before this marker. Leave empty for no cropping.                                                                                                 |
| **strip\_html\_comments** | Boolean               | Strip out the hidden HTML comments from the commit message when merging the PR. Defaults to `false`.                                                                                                                                         |
| **include\_coauthors**    | Boolean               | Include coauthors (if any) in the commit message when merging the PR. Defaults to `true`.                                                                                                                                                    |
| **apply\_title\_regexes** | List\[ReplacePattern] | Contains the strings `pattern` and `replace` to indicate what pattern to replace in the PR title. This will be applied to the merge commit. In parallel mode, this also applies to the draft PR title. See the example tab for more details. |
{% endtab %}
{% endtabs %}

### Merge Strategy

| Name     | Type   | Description                                                                                                                                                                                                                                                                        |
| -------- | ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **name** | String | Defines the merge strategy to use, the options are "squash", "merge", and "rebase". See the [GitHub docs](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/configuring-pull-request-merges/about-merge-methods-on-github) for more info. |

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
```

{% tabs %}
{% tab title="Attributes" %}
| Name       | Type   | Description                                                            |
| ---------- | ------ | ---------------------------------------------------------------------- |
| **squash** | String | If marked with this label, the PR will be squashed and merged.         |
| **merge**  | String | If marked with this label, the PR will be merged using a merge commit. |
| **rebase** | String | If marked with this label, the PR will be rebased and merged.          |
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
| **publish**          | String | One of `always`, `queued`, or `never`. Defaults to `always`.                                             |
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
  enable_comments: true
  ci_timeout_mins: 60
  base_branches:
    - master
    - /release-*/
```

{% tabs %}
{% tab title="Attributes" %}
| Name                           | Type          | Description                                                                                                                                                                                                                                                                                                        |
| ------------------------------ | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **update\_latest**             | Boolean       | Determines whether Aviator bot will merge the latest base branch into the current branch of your PR before verifying the CI statuses. This is only compatible with the `default` and `no-queue` merge modes. Defaults to `true`.                                                                                   |
| **delete\_branch**             | Boolean       | Determines whether Aviator bot will delete the branch after merging. Defaults to `false`.                                                                                                                                                                                                                          |
| **use\_rebase**                | Boolean       | Determines if Aviator bot will use rebase to update PRs. This feature is only available in the Pro plan. Please note that you also need to enable `update_latest` for rebase to work. Defaults to `false`.                                                                                                         |
| **publish\_status\_check**     | Boolean       | Determines if Aviator bot will publish a status check back on the PR. This status check represents the current state of PR in the Aviator queue.                                                                                                                                                                   |
| **enable\_comments**           | Boolean       | Determines if Aviator bot can add comments on the PR to describe the actions performed on the PR by the bot. Aviator bot comments include information such as failure reasons and the position of the PR in the queue. Defaults to `true`.                                                                         |
| **ci\_timeout\_mins**          | Integer       | The time before we determine that the CI has timed out. Defaults to no time out.                                                                                                                                                                                                                                   |
| **base\_branches**             | List\[String] | These branches are the ones that Aviator will monitor as valid branches to merge into. Defaults to your repository default branch as configured on GitHub. Regexes are allowed.                                                                                                                                    |
| **require\_all\_checks\_pass** | Boolean       | Determines if Aviator will enforce all checks to pass. If true, any single failing test will cause the PR to fail. These checks include the ones we receive status updates for via GitHub. This may work well if your repo has conditional checks. Requires at least one check to be present. Defaults to `false`. |
{% endtab %}
{% endtabs %}
