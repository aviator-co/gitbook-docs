---
description: >-
  Learn the nuances of batching and related FAQs. Batching works alongside
  Parallel mode to scale the throughput of PRs merged, by merging more PRs per
  CI run.
---

# Batching

Batching works alongside Parallel mode to increase the throughput of PRs merged, by merging more PRs per CI run.

## How it works

### Config Settings

These settings control batching in your configuration file.

`batch_size` = This is the number of queued PRs batched together for a draft PR CI run. Defaults to 1.

`batch_max_wait_minutes`= The time to wait before creating the next batch of PRs, e.g. if `batch_size` = 3 but there are only currently 2 queued PRs, then a batch of those 2 PRs will be created after `batch_max_wait_minutes`. The wait is measured from when the last draft PR was created, so it only applies while a draft PR is in flight. Defaults to 0.

`batch_wait_when_idle` = If enabled, `batch_max_wait_minutes` also applies when no draft PR is in flight, measured from when the oldest waiting PR was queued. Defaults to `false`, which tests the first PR on an idle queue right away. See [Waiting on an idle queue](#waiting-on-an-idle-queue).

```yaml
version: 1.0.0
merge_rules:  
    labels:    
        trigger: "label_name"
    merge_mode:
        type: "parallel"
        parallel_mode:
            batch_size: 3
            batch_max_wait_minutes: 0
            batch_wait_when_idle: false
```

### Waiting on an idle queue

By default, `batch_max_wait_minutes` only holds PRs back while a draft PR is already running. When the queue is idle, the first PR to arrive gets its own draft PR immediately, and PRs queued a few seconds later wait for the next batch. With bursty traffic, this often produces a batch of one followed by a full batch.

Set `batch_wait_when_idle: true` to wait for a fuller batch on an idle queue too:

```yaml
merge_mode:
  type: "parallel"
  parallel_mode:
    batch_size: 4
    batch_max_wait_minutes: 5
    batch_wait_when_idle: true
```

With this config, when nothing is in flight, Aviator waits up to 5 minutes from when the oldest PR was queued before creating a draft PR.

* A full batch (`batch_size` PRs) is created right away, without waiting.
* `skip_line` PRs are never held.
* PRs requeued after a [bisection](#bisection) skip the wait, since they already waited once.
* `skip_draft_when_up_to_date` still applies once the wait ends.
* Waiting is tracked per base branch.

This trades a few minutes of latency on a quiet queue for fewer CI runs. It has no effect when `batch_max_wait_minutes` is 0.

### Current workflow

Here is the current workflow of parallel mode. It defaults to `batch_size`=1 since each additional CI pipeline will combine the changes from 1 PR and all the previous PRs in the queue.

![Workflow with batch\_size=1.](<../../../.gitbook/assets/Screen Shot 2022-04-01 at 11.23.26 AM.png>)

### Workflow with batching

By adjusting `batch_size`, your repo can merge PRs faster with reduced CI runs. Let’s look at an example where `batch_size=3` and we have 6 total tagged PRs.

Pipeline #1 will batch together the changes from PR#1, PR#2, and PR#3. Once the CI passes, PRs 1-3 will be merged.

Pipeline #2 will batch together the changes from Pipeline #1 and PR#4, PR#5, and PR#6. Once the CI passes, PRs 4-6 will be merged.

![Workflow with batch\_size=3.](<../../../.gitbook/assets/Screen Shot 2022-04-01 at 2.16.32 PM.png>)

By increasing the `batch_size` from 1 to 3, we can merge 6 PRs vs 2 PRs in the same time it takes to run 2 CI pipelines.

Here’s a quick comparison of how many additional CI pipelines\* are run with different batch sizes for a **queue with 10 PRs**.

\*Additional CI pipelines are the CI runs in addition to the CI that runs on the original PR.

<table><thead><tr><th width="160"></th><th>batch_size = 1</th><th>batch_size = 3</th><th>batch_size = 5</th></tr></thead><tbody><tr><td>Additional CI Pipelines</td><td>10</td><td>4</td><td>2</td></tr></tbody></table>

### Handling Failures

#### Bisection

It's inevitable that we will run into build failures - we handle this by requeuing PRs into bisected batches.

For example, let's say our `batch_size`=5 and we have PRs #1-5 in a single batch, with the corresponding draft PR #6.

If the draft PR fails, then we will close draft PR #6, and requeue all of PRs #1-5. These PRs will be put into two bisected batches - the first batch contains PRs #1-3, the second batch contains PRs #4-5.

![Bisection if draft PR fails.](<../../../.gitbook/assets/Screen Shot 2022-05-26 at 1.16.35 PM.png>)

If a PR in the batch fails, we similarly requeue with bisection. In the above example, if PR #1 fails, then we block PR #1 and requeue PRs #2-5. Those PRs will be requeued into two batches - PRs #2-3 in one batch, PRs #4-5 in the other.

#### Other FAQs

<details>

<summary>How do you handle PRs with a skip_label?</summary>

All PRs with skip labels will be prioritized. They will be bumped to the top of the queue and will always be in its own batch. PRs with a skip\_label will never be batched with other PRs.

</details>

<details>

<summary>What happens if I dequeue a PR?</summary>

The corresponding draft PRs as well as any subsequent draft PRs will be closed. PRs in the current batch will be requeued.

</details>

<details>

<summary>What happens if I add a new commit?</summary>

If you add a new commit to a PR, the associated draft PR will be closed and all subsequent draft PRs will be reset. The PRs in the batch will be automatically requeued.

</details>

<details>

<summary>What happens if my PR fails to merge but other PRs in the batch succeed?</summary>

If your PR fails to merge, we will automatically retry merging again. If it still fails, the PR will be blocked and may require manual changes.

</details>

<details>

<summary>What happens if I close the draft PR associated with multiple PRs?</summary>

If you manually close a draft PR, this will trigger a full reset of the queue. All draft PRs will be closed, and all PRs will be requeued.

</details>

<details>

<summary>What happens if I close my PR?</summary>

If you manually close a PR that has been queued and has an associated draft PR, this will trigger a full reset of the queue. All draft PRs will be closed and all PRs are requeued.

</details>
