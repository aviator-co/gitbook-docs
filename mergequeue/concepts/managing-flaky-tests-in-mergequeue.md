---
description: >-
  Learn about the flaky test problem in parallel mode and how to manage flaky
  tests with optimistic validation. Optimistic validation configuration
  instructions.
---

# Managing flaky tests

Optimistic validation is a feature to mitigate the queue reset caused by flaky tests in [<mark style="color:blue;">parallel mode</mark>](parallel-mode/). When a test fails in a batch, it waits for other batches to see if a test passes.

## Flaky test problem in parallel mode

In parallel mode, queued PRs are tested in a batch.

<figure><img src="../../.gitbook/assets/Screen Shot 2023-07-16 at 11.58.37 AM.png" alt=""><figcaption><p>Parallel mode with two PRs</p></figcaption></figure>

For example, when there are two PRs (`PR #1` and `PR #2`) enqueued, we create two draft PRs (`Draft PR #3` and `Draft PR #4`) for testing. These two draft PRs are based on the latest `main` branch and run tests to check tests won't fail after merge. `Draft PR #3` includes `PR #1` and `Draft PR #4` includes `PR #1` and `PR #2`. This way, the tests of these PRs can run in parallel, making sure that the post-merge state still pass the tests.

When a CI run fails in one of those parallel build draft PRs, that failing a PR gets dequeued. This causes a queue reset. Imagine that `Draft PR #3` fails the CI run. This includes `PR #1` and this PR gets dequeued. `Draft PR #4` also includes `PR #1` and the CI run for this PR is cancelled. Because all the PRs created after `PR #1` includes it, this makes a cascading effect and effectively the entire queue is reset.

If your test is flaky, this queue reset happens frequently. This slows down the entire queue and affects the efficacy of the team.

## Optimistic validation

Optimistic validation is a feature to address this situation. When a CI run fails for a draft PR, instead of immediately dequeue the PR and causing a queue reset, it waits for other PRs to finish.

Continue from the example above. When a CI run for `Draft PR #3` fails, without optimistic validation, it dequeues the `PR #1` and resets the entire queue. With optimistic validation, it waits for other draft PRs (in this case `Draft PR #4`) to finish the CI runs. When they pass the CI, it assumes that the CI run would have been passed and merges the PRs.

## Configuring optimistic validation

There are two settings to configure optimistic validation. Both are specified within the parallel mode section of the configuration.

`use_optimistic_validation` - boolean value that configures only the look-ahead of CI. If the CI of the top of the PR fails, it will still immediately dequeue the PR. Defaults to `true`.

`optimistic_validation_failure_depth` - integer value that how many PRs, including the failing PR itself, it will check for the optimistic validation. Setting `1` effectively means that it won't use optimistic validation. This is capped to 3 in order to cap the GitHub API calls. Requires `use_optimistic_validation` to be enabled.

```
merge_rules:
  labels:
    trigger: "mergequeue"
  merge_mode:
    type: "parallel"
  parallel_mode:
    max_parallel_builds: 10
    use_optimistic_validation: true
    optimistic_validation_failure_depth: 2
```

## Side-effect of optimistic validation

By introducing optimistic validation, real errors will take longer to be kicked out of the queue. This is because when there is a CI run failure, it keeps waiting for other batches to finish. Therefore, the failure depth should generally be kept small.

Most of the Aviator users find this side-effect is acceptable as the number of real failures in the queue are relatively small. However, this depends on how flaky your tests are.
