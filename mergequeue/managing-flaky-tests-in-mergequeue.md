# Managing Flaky Tests in MergeQueue

Flaky tests can be extremely frustrating for the health of your CI. These can especially be deteriorating when you want to incorporate a merge queue in your development workflow. Aviator MergeQueue offers capabilities to reduce the impact of flaky tests on your queue.

## Optimistic validation

To understand the queue workflow around flaky tests, it’s worth first understanding how optimistic validation works. Note that these capabilities specifically work in [<mark style="color:blue;">parallel mode</mark>](concepts/parallel-mode/), including all variations of parallel mode ([<mark style="color:blue;">affected targets</mark>](concepts/affected-targets/), [<mark style="color:blue;">fast forwarding</mark>](how-to-guides/fast-forwarding.md), and [<mark style="color:blue;">batching</mark>](concepts/parallel-mode/batching.md)).

Consider a simple example of parallel mode where Aviator creates Draft PRs that combine changes from one or more queued PRs. These Draft PRs run the CIs in parallel to reduce the wait time. Let’s say there are two PRs `PR #1` and `PR #2` that are queued, and corresponding draft PRs are created such that draft `PR #3` has changes from `PR #1` and draft `PR #4` have changes from `PR #1` and `PR #2`.

<figure><img src="../.gitbook/assets/Screen Shot 2023-07-16 at 11.58.37 AM.png" alt=""><figcaption><p>Parallel mode with two PRs</p></figcaption></figure>

In a standard MergeQueue workflow, if the CI of `PR #3` pass we will merge `PR #1`, and then when the CI of `PR #4` pass we merge `PR #2`. Now when optimistic validation is enabled, Aviator validates the status checks of both `PR #3` and `PR #4` together. In this case, if CI validation of `PR #4` passes before `PR #3`, Aviator will merge both `PR #1` and `PR #2` as the combined changes are valid even though the CI of `PR #3` is not completed.

{% hint style="info" %}
To keep the API limits in order, Aviator analyzes only the top 3 PRs in the queue.
{% endhint %}

## Flaky tests

This optimistic validation approach builds the foundation of managing flaky tests in MergeQueue. Alongside enabling optimistic validation, you can also specify the `optimistic_validation_failure_depth` rule that informs Aviator to not dequeue a failed CI immediately. So, let’s again look at the example above.

Let’s say you set the `optimistic_validation_failure_depth` to `2`. And in the example above, if some tests are flaky, causing the CI on `PR #3` to fail, Aviator will not immediately dequeue `PR #1` and reset the queue. Instead, we will wait for `2` consecutive CI to fail before dequeuing. So if later the CI of `PR #4` passes, we use the same optimistic logic above to merge both `PR #1` and `PR #2` creating eventual consistency.

Resets in a merge queue can impact the performance (time to merge) for the PRs. By introducing some resistance for failed tests, you can significantly improve the overall time to merge in your queue.

Note that there is a side effect of this configuration. By introducing a failure depth, real errors will take longer to be kicked out of the queue. Therefore, the failure depth should generally be kept small. Although, We have noticed with most of the users of Aviator that this side effect is typically acceptable as the build failures in the queue are relatively small. Eventually it also depends on how flaky your tests are.

### Configuring optimistic validation

There are two settings to configure optimistic validation. Both are specified within the parallel mode section of the configuration.

`optimistic_validation` - boolean value that configures only the look-ahead of CI. If the CI of the top of the PR fails, it will still immediately dequeue the PR. Defaults to `true`.

`optimistic_validation_failure_depth` - integer value that defines how many subsequent failures are required to dequeue a PR. Requires `optimistic_validation` to also be enabled.

Example:

```
merge_rules:
  labels:
    trigger: "mergequeue"
  merge_mode:
    type: "parallel"
  parallel_mode:
    max_parallel_builds: 10
    optimistic_validation: true
    optimistic_validation_failure_depth: 2
```

## TestDeck

If you are looking for an end-to-end capability to manage flaky tests in your workflow, also checkout [<mark style="color:blue;">TestDeck</mark>](managing-flaky-tests-in-mergequeue.md#testdeck).\
