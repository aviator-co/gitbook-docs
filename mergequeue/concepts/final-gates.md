---
description: >-
  Learn how final gates run a GitHub Actions workflow as the last step before
  MergeQueue merges a pull request, and how to configure them.
---

# Final Gates

Final gates let you run a GitHub Actions workflow as the **last step before a PR is merged** by MergeQueue. The gate fires only once the PR has reached the top of the queue and all of its required checks have passed. If the gate succeeds, the PR is merged. If it fails or times out, the PR is removed from the queue.

This is useful when you need a job to run **once per merge, just-in-time**. For example:

* Running a database migration before the code that depends on it lands.
* A short pre-merge validation that is too expensive to run on every PR commit.
* Any workflow that should execute exactly once, in order, against the PR that is about to merge.

{% hint style="info" %}
Final gates require [parallel mode](parallel-mode/README.md). They are not evaluated in the `default` or `no-queue` merge modes.
{% endhint %}

## Configuration

Add a `final_gates` list to `merge_rules` in your `.aviator/config.yml` (or through the merge rules UI). Each entry references a workflow file in your repository and a timeout.

```yaml
merge_rules:
  final_gates:
    - workflow: run-migration.yml
      timeout_mins: 10
      label: has-migration
    - workflow: pre-merge-validation.yml
      timeout_mins: 5
```

<table><thead><tr><th width="180">Name</th><th width="120">Type</th><th>Description</th></tr></thead><tbody><tr><td><strong>workflow</strong></td><td>String</td><td>Required. Filename of the workflow under <code>.github/workflows/</code>. Either <code>run-migration.yml</code> or <code>.github/workflows/run-migration.yml</code> is accepted; nested paths are not.</td></tr><tr><td><strong>timeout_mins</strong></td><td>Integer</td><td>Required. How long to wait for the workflow run before timing out. Valid range: <code>1</code> to <code>60</code>.</td></tr><tr><td><strong>label</strong></td><td>String</td><td>Optional. Only run this gate when a PR being merged carries this label. When omitted, the gate runs for every merge. Matched case-insensitively.</td></tr></tbody></table>

You can configure multiple final gates. They run **sequentially in the order listed**: gate 2 only starts after gate 1 succeeds.

### Scoping a gate to a label

Most gates only matter for a subset of changes. Setting `label` restricts a gate to PRs carrying that label, so the rest of your queue is not slowed down by it.

With [batching](parallel-mode/batching.md), a gate applies if **any** PR in the batch carries the label. A safety gate should run whenever a label-relevant change is present, even when it is batched alongside unlabeled PRs.

If no PR in the batch carries the label, the gate is skipped entirely — no workflow is dispatched and no comment is posted — and MergeQueue moves on to the next gate or to the merge.

## When does the gate fire?

A final gate fires when all of the following are true:

1. The PR (or batch) is at the top of the merge queue and is the next one scheduled to merge.
2. All of the required checks have passed on its draft PR.
3. The gate applies, per its optional `label`.

This guarantees the gate runs at most once per merge attempt, and only when a merge is genuinely imminent.

## Workflow requirements

The workflow you reference **must declare `workflow_dispatch` as a trigger**. MergeQueue invokes it through GitHub's workflow dispatch API, against the draft PR's branch.

```yaml
# .github/workflows/run-migration.yml
name: Run Migration
on:
  workflow_dispatch:
    inputs:
      associated_prs:
        description: "JSON-encoded list of PR numbers being merged"
        required: true
      head_sha:
        description: "Commit SHA of the draft PR being evaluated"
        required: true

jobs:
  migrate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          ref: ${{ inputs.head_sha }}
      - name: Log associated PRs
        run: echo "Running migration for PRs ${{ inputs.associated_prs }}"
      - run: ./scripts/run-migration.sh
```

MergeQueue passes the following inputs to every dispatched workflow:

* `associated_prs` — JSON-encoded list of PR numbers represented by this draft PR. In non-batched merges this contains a single PR; in batched merges it contains every PR in the batch. Example: `"[123, 124]"`. Parse it with `${{ fromJSON(inputs.associated_prs) }}`.
* `head_sha` — the head commit SHA of the draft PR. Use this to check out the exact code mix being validated. It is passed explicitly so the workflow has an authoritative value even if the branch shifts.

## Behavior

**Success.** The workflow run completes with a `success` conclusion within the timeout. MergeQueue proceeds to the next gate, if any, and then merges.

**Waiting.** While a gate is in flight, MergeQueue posts a comment on the draft PR noting which gate it is waiting on and the configured timeout, and re-checks the run on each queue tick.

**Failure.** The workflow run completes with any non-success conclusion (`failure`, `cancelled`, and so on). MergeQueue blocks the PRs in the batch and removes them from the queue, with a message such as:

> Final gate `run-migration.yml` failed with conclusion `failure`. See workflow run: \[link]

The next PR in the queue then becomes eligible for its own final gate.

**Timeout.** The workflow run does not complete within `timeout_mins`. MergeQueue treats this as a failure and blocks the batch with a timeout message and a link to the run.

**Dispatch problems.** If the workflow cannot be dispatched — for example GitHub rejects the request, the workflow file does not exist, or no matching run appears — MergeQueue fails closed and blocks the batch rather than merging unvalidated code.

Gate failures are recorded in the [audit trail](audit-trail.md) with a dedicated status code, so you can distinguish them from CI failures.

**Manual commits to the draft PR.** If someone pushes commits directly to a draft PR while a final gate is in flight, MergeQueue marks the PRs as blocked, as it already does today. The gate result is discarded and the PRs must be re-queued.

**Additional CI failures.** Likewise, if a check that was previously passing re-runs on the code mix SHA and fails, the PRs are marked as blocked.

## Recommendations

* **Make your gate workflow idempotent.** MergeQueue dispatches each gate once per merge attempt, but idempotency protects you against edge cases such as re-queuing a PR after a gate failure, or an Aviator retry after a slow GitHub API call. For database migrations, use a framework that tracks applied migrations (Alembic, Flyway, Liquibase, Rails, Django) — they all handle this natively.
* **Keep gates short.** Final gates serialize the very last step of merging, so a slow gate reduces overall queue throughput. Aim to keep each gate under a few minutes.
* **Set realistic timeouts.** A timeout that is too short will kick PRs whose gates would have succeeded; one that is too long delays detection of a stuck workflow. Pick a value comfortably above the gate's typical runtime.
* **Scope gates with `label`.** A gate that only matters for a subset of changes should carry a label so it does not add merge latency to unrelated PRs.
* **Use a single gate where you can.** Multiple gates are supported, but they multiply merge-time latency. Combine related checks into one workflow when possible.
