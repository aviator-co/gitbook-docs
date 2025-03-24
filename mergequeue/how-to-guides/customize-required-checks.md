---
description: >-
  Learn how to customize required checks in our guide for MergeQueue. Discover
  several main approaches and use the most suitable and convenient one for your
  team.
---

# How to Customize Required Checks

You can configure required checks in a few different ways.

## Use GitHub mergeability

This is the default setting. When this configuration is enabled, Aviator will fetch the required checks from GitHub’s branch protection rules. These are also the default required checks for validating draft PRs in parallel mode.

```
merge_rules:
  labels:
   trigger: "mergequeue"
  preconditions:
    use_github_mergeability: true
```

## Custom required\_checks

You can alternatively specify a set of separate required checks. This may be useful if you want additional validation before merging your PR, or if you have different requirements for various base branches. The custom checks can be specified from the merge rules UI or from the config file.

### Specifying from the merge rules UI

Aviator already pre-populates all the checks from your repository here and default selects the required GitHub checks.

<figure><img src="../../.gitbook/assets/Screen Shot 2022-12-29 at 9.14.58 AM.png" alt=""><figcaption><p>Customize required checks modal</p></figcaption></figure>

### Specifying from config file

While specifying in the config file, you should follow the naming convention as specified in your GitHub pull requests. Typically it is the full bolded name in the PR. Example:

<figure><img src="../../.gitbook/assets/Screen Shot 2023-04-17 at 9.52.06 AM.png" alt=""><figcaption><p>status checks from GitHub Pull Request</p></figcaption></figure>

So in this case, the check for the last CircleCI test will be `ci/circleci: pytest`, but for the case of GitHub actions, only the job name is used (after forward slash). So the above check names would be in-order:

```yaml
merge_rules:
  preconditions:
    use_github_mergeability: false
    required_checks:
      - build
      - unit-test
      - typecheck
      - ci/circleci: pytest
```

## Using wildcards

You can use wildcards (\*) while specifying required CI checks. Example:

```yaml
required_checks:     
  - "ci/circleci: check_1"
  - "golang-*"  # matches golang-test, golang-lint, etc.
```

With this wildcard, Aviator will validate all checks with names matching this expression. This validation requires at least one matching check to be present.

## Conditional checks

You can set custom acceptable statuses in the required check to support additional check statuses other than the default success.

In the below example, Aviator will only consider the check unit-test as passing if we receive a success status. But for the check named _conditional\_build_, Aviator will also consider a skipped status as passing. You can see all possible `acceptable_statuses` in the [<mark style="color:blue;">reference doc</mark>](../reference/complete-reference-guide.md#acceptable_statuses). These map to the statuses defined by GitHub [<mark style="color:blue;">here</mark>](https://docs.github.com/en/rest/checks/runs#get-a-check-run).

```
merge_rules:
  labels:
    trigger: mq
  preconditions:
    required_checks:
      - unit-test
      - name: conditional_build
        acceptable_statuses:
          - success
          - skipped
```

## Optional checks

There are scenarios where a check is only run in certain conditions. In such cases, you would want to validate the check when it runs, but also accept the state when the check is not run at all. For this particular scenario, you can define the acceptable\_statuses as success and missing:

```
merge_rules:
  labels:
    trigger: mq
  preconditions:
    required_checks:
      - name: conditional_build
        acceptable_statuses:
          - success
          - missing
```

## Override checks (for parallel mode)

You can customize the required checks for [<mark style="color:blue;">parallel mode</mark>](https://docs.aviator.co/mergequeue/concepts/parallel-mode) using the Merge Rules UI or via the config file. You can read more [<mark style="color:blue;">here</mark>](https://docs.aviator.co/mergequeue/concepts/parallel-mode).

Here’s an example to do this in the config file:

```
  merge_mode:
    type: "parallel"
    parallel_mode:
      override_required_checks:
        - build_and_test
        - 'ci/circleci: build'
```

## Require all checks passing

You can also configure Aviator to enforce all checks to pass. If true, any failing test will cause the PR to be dequeued. This may work well if your repo has conditional checks. Requires at least one check to be present. You can set this both for the original PR and the draft PR.

```yaml
merge_rules:
  labels:
    trigger: "label_name"
  require_all_checks_pass: true
  merge_mode:
    type: "parallel"
    parallel_mode:
      require_all_draft_checks_pass: true

```
