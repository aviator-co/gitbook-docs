---
description: >-
  Learn how Aviator decides whether a CI failure in the merge queue is a flake
  worth retrying or a real breakage worth dequeuing, using deterministic
  evidence first and an LLM only as the last step.
---

# Flaky test detection \[Beta]

When a CI check fails on a draft PR, Aviator can investigate the failure before deciding what to do about it. If the failure looks like it would pass on a retry, Aviator reruns the check and keeps the pull request in the queue instead of dequeuing it and resetting everything behind it.

{% hint style="info" %}
Flaky test detection is in beta. It is off by default and must be enabled per repository. See [How to configure flaky test detection](../how-to-guides/configure-flaky-test-detection.md).
{% endhint %}

## The problem

In [parallel mode](parallel-mode/), a failing draft PR dequeues its pull request and cascades into a queue reset. [Optimistic validation](managing-flaky-tests-in-mergequeue.md) softens this by waiting to see whether a later batch passes, but it treats every failure the same way: it has no idea *why* the check failed.

That is the gap this feature fills. A test that fails because a container image could not be pulled and a test that fails because the code is broken are both just "red" to the queue. Flaky test detection reads the failure and tells them apart.

## How Aviator decides

Every failure runs through the same ladder. Each step can answer on its own, and Aviator stops at the first one that does, so the expensive steps only run when the cheap ones are inconclusive.

```mermaid
flowchart TD
    A[Check fails on a draft PR] --> B{Is the check<br/>opted in?}
    B -- no --> Z[Dequeue as usual]
    B -- yes --> C{Can the failure<br/>output be read?}
    C -- no --> Z
    C -- yes --> D{Does the failure name<br/>a file this batch changes?}
    D -- yes --> Z
    D -- no --> E{Is this failure already<br/>known from a past build?}
    E -- yes --> F[Reuse the earlier verdict]
    E -- no --> G{Is the same failure hitting<br/>unrelated batches too?}
    G -- yes --> H[Treat as a repository-wide<br/>incident, not this PR's fault]
    G -- no --> I{Would a rerun of this<br/>same code pass?}
    I -- "no, or unclear" --> Z
    I -- yes --> J[Keep the PR queued<br/>and rerun the check]
    F --> J
    H --> J
```

### 1. Only checks you opt in

Aviator investigates the checks you list in `retriable_checks` and nothing else. A check you have not named is dequeued exactly as it is today.

### 2. Reading the failure

Aviator fetches the failed job's output and extracts the part that describes the failure, skipping the setup and teardown noise that surrounds it. If the log cannot be fetched, or nothing in it identifies a failure, the investigation stops. **No log means no verdict** — Aviator will not guess.

### 3. Deterministic evidence first

If the failure output names a file the batch modifies, the failure is treated as related to the change and the PR is dequeued. This is a plain comparison of paths, it costs nothing, and it resolves a large share of real breakages without any model involved.

### 4. Failure identity and history

Aviator computes a stable identity for each failure from its structure — which test failed, what kind of error it was, and which of your own source frames it passed through — rather than from the raw text, so the same failure is recognised across runs even though timestamps, durations and process ids differ.

That identity does two things. A failure Aviator has already classified reuses that verdict instead of being investigated again. And when the same identity appears across batches that share no pull requests, the failure cannot be any one PR's fault — that is a repository-wide incident, and Aviator says so rather than blaming whichever PR happened to be at the front.

### 5. The model, last

Only when the deterministic steps are inconclusive does Aviator ask a model, and it asks exactly one question:

> Would this check pass if it were run again on the same code?

Not "did this change cause it". That distinction matters. A test broken on your main branch is not this pull request's fault, but it fails identically every time, so rerunning it only keeps a doomed batch alive. Aviator retries failures that are **transient**, not failures that merely belong to somebody else.

The model sees the failure output, the list of files the batch changes, and any [context you have written](../how-to-guides/configure-flaky-test-detection.md#giving-aviator-context) about how this check tends to fail. It can also pull up the diff of any individual file when it needs to.

## What happens to a transient failure

Two things, together:

**The failure stops counting against the queue's patience.** Optimistic validation allows a limited number of failures before dequeuing, set by `optimistic_validation_failure_depth`. A failure judged transient does not consume one of those, so the queue keeps looking ahead rather than giving up on the PR.

**The failed check is run again.** Aviator asks your CI provider to rerun the specific job that failed, not the whole workflow. If it passes, the batch carries on as though the flake never happened.

## What Aviator will not do

The safety properties matter more than the feature itself, so they are worth stating plainly.

* **It never dequeues a PR sooner than it would today.** The configured failure depth is a floor. A verdict can only ever make the queue more patient.
* **It never retries a deterministic failure.** If the failure would fail the same way next time, it is dequeued, whether or not this change caused it.
* **Uncertainty changes nothing.** If the log cannot be read, the model is unavailable, or the answer is unclear, Aviator falls back to exactly the behaviour you have today.
* **It never marks a check as passing.** Aviator reruns the check and lets your CI decide. A rerun that fails again is a failure.

## Tracking flakiness over time

Whenever a check fails and then passes on the same commit, Aviator records that as an observed flake. The commit did not change, so the code cannot explain the disagreement.

These observations accumulate into a flake rate per check, which is available in your analytics and is fed back into the investigation as prior evidence: a check with a long history of flaking is treated differently from one that has never flaked before. It also gives you a straightforward way to find your worst offenders and fix them, which remains the real solution.

## Supported CI providers

| Provider | Reading failures | Retrying |
| -------- | ---------------- | -------- |
| GitHub Actions | Supported | Reruns the failed job and its dependent jobs |
| Buildkite | Supported | Retries the failed step |

For GitHub Actions, Aviator reruns the individual failing job rather than the workflow run, so matrix legs that already passed are not repeated. Jobs that declare a `needs:` dependency on the failed job are rerun with it.

Checks that report as commit statuses rather than check runs can be investigated, but cannot be retried automatically — there is no per-job handle to retry. For those, Aviator can still extend the queue's patience.

## Limitations

* A failure with no readable log cannot be investigated.
* A test that fails for a genuine but unrelated reason — broken on main, for example — is correctly not retried, and will still dequeue the PR.
* Retrying costs CI time. A check that flakes constantly is better fixed than retried, which is what the flake rate is there to tell you.
