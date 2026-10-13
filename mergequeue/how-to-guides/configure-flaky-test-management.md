---
description: >-
  Learn how to configure flaky test management for MergeQueue: choosing which
  checks can be retried, and giving Aviator context about how your CI tends to
  fail.
---

# How to Configure Flaky Test Management

Flaky test management lets Aviator investigate a failing check on a draft PR and, when the failure looks transient, rerun the check and keep the pull request queued instead of dequeuing it.

This guide covers setting it up. For how Aviator decides a failure is flaky, what it does about it, and what it will never do, read [Flaky test management](../concepts/flaky-test-management.md) first.

{% hint style="info" %}
This feature is in beta and is off by default. It applies to [parallel mode](../concepts/parallel-mode/) only.

During beta it also has to be enabled on your account by Aviator — the configuration below has no effect until it is. Contact your Aviator representative or support@aviator.co to be added.
{% endhint %}

## Getting started

The rollout is three steps, and the first two change nothing about how your queue behaves.

### 1. Turn it on in shadow mode

Pick one repository and one check you already know is flaky:

```yaml
merge_rules:
  labels:
    trigger: "mergequeue"
  merge_mode:
    type: "parallel"
  flaky_retry:
    enabled: true
    shadow_mode: true
    retriable_checks:
      - name: "integration-tests"
```

In [shadow mode](#shadow-mode) Aviator investigates failures and records what it concluded without acting on any of it. No check is rerun, and no pull request survives a failure it would be dequeued for today. This is the mode to sit in while you decide whether you trust the verdicts.

### 2. Read what it concluded

Two places to look, and they answer different questions.

**The comment on the draft PR** explains one decision. Each time Aviator would have treated a failure as flaky it comments on the draft, naming the check, quoting its reasoning, and saying plainly that nothing was changed. See [what Aviator posts](#what-aviator-posts-on-the-pull-request).

**The flakiness dashboard** shows the pattern across decisions: every distinct failure Aviator has seen in the repository, how often each has recurred, and which commits it appeared on. Find it at `https://app.aviator.co/repos/<org>/<repo>/flakiness`, or follow the **Flake history** link in any comment Aviator posts.

<!-- TODO(screenshot): the flakiness dashboard for a repository, showing the list of
     failure signatures with their recurrence counts. Replace this comment with:
     <figure><img src="../../.gitbook/assets/<filename>" alt=""><figcaption><p>The
     flakiness dashboard lists each distinct CI failure Aviator has seen, with how
     often it has recurred.</p></figcaption></figure> -->

Spend a week here. What you are looking for is a verdict you disagree with — a failure Aviator called flaky that you know is genuine. Most corrections are one sentence of [context](#giving-aviator-context) naming that failure mode as real.

### 3. Turn shadow mode off

When the verdicts look right, remove the one line:

```yaml
  flaky_retry:
    enabled: true
    retriable_checks:
      - name: "integration-tests"
```

From here Aviator reruns the checks it exonerates and keeps those pull requests queued. Add further checks once the first is behaving.

## Minimal configuration

Enable it and name at least one check that is allowed to be retried:

```yaml
merge_rules:
  labels:
    trigger: "mergequeue"
  merge_mode:
    type: "parallel"
  flaky_retry:
    enabled: true
    retriable_checks:
      - name: "integration-tests"
```

A check that is not listed is never investigated and never retried.

This is the configuration to arrive at, not to start from — add `shadow_mode: true` while you are still evaluating the verdicts.

## Shadow mode

`shadow_mode` runs the whole investigation and records every verdict, but never lets one change a queue decision:

```yaml
  flaky_retry:
    enabled: true
    shadow_mode: true
    retriable_checks:
      - name: "e2e-*"
```

It exists so you can judge the verdicts on your own CI before they are allowed to matter. Reading a week of recorded decisions is a far better basis for trusting this than reading the documentation for it.

**What still happens.** Failures are investigated, verdicts are recorded, failure signatures accumulate, and the flakiness dashboard fills up. When a verdict would have changed what the queue did, Aviator comments on the draft PR to say so.

**What does not.** No check is rerun. No failure is exonerated, so your `optimistic_validation_failure_depth` budget is spent exactly as it is today and every pull request is dequeued exactly when it would be with the feature off.

{% hint style="info" %}
Shadow mode comments on the **draft PR only**, not on the pull requests in the batch. Nobody needs to act on a decision that was not taken, so the authors are left alone. Once you turn shadow mode off, the comment goes to every pull request in the batch, because then they really are all waiting on that one failure.
{% endhint %}

### What it costs

Two things, both small, but neither is zero.

Investigating a failure reads CI logs and usually asks a model, so shadow mode spends that on every failure of a listed check while returning no queue benefit. Keep `retriable_checks` to the one or two checks you are evaluating rather than listing everything.

Handling a failure is also held briefly while the verdict is reached — up to two minutes, and typically much less. A batch in shadow mode is therefore dequeued slightly later than it would be with the feature off entirely. It is dequeued, and for the same reason; only the timing differs.

## Choosing which checks

`retriable_checks` accepts glob patterns, which is useful when your CI generates one check per shard or per matrix leg:

```yaml
  flaky_retry:
    enabled: true
    retriable_checks:
      - name: "e2e-*"
      - name: "integration-tests"
      - name: "buildkite/acme/browser-tests"
```

Use the check name exactly as it appears on your pull request. For GitHub Actions this is the job name; for Buildkite it is typically `buildkite/<pipeline>/<step-key>`.

{% hint style="warning" %}
A check that never fails deterministically is a poor candidate. Retrying a test that is genuinely broken wastes CI time and delays the feedback that it is broken. Pick candidates from the checks your team already reruns by hand, and from the flakiness dashboard once it has data.
{% endhint %}

## Giving Aviator context

The single highest-leverage thing you can configure is a short description of how a check actually fails. Aviator reads it at the [last step of the decision ladder](../concepts/flaky-test-management.md#how-aviator-decides), and it is often more informative than the diff.

```yaml
  flaky_retry:
    enabled: true
    retriable_checks:
      - name: "e2e-*"
        context: |
          These tests run against ephemeral preview environments. Timeouts
          waiting for the environment to become ready, and 502s from the
          preview host, are infrastructure and clear on a retry.
          Assertion failures in page content are genuine.
```

Write it the way you would explain the check to a new teammate: what tends to go wrong that is not the code's fault, and what is always real. Being explicit about the *genuine* failure modes matters as much as the flaky ones — it stops Aviator retrying failures it should not.

### Repository-wide context

Context can also be set once for the whole repository. It applies **in addition to** any per-check context, not instead of it, so shared infrastructure only needs describing once:

```yaml
  flaky_retry:
    enabled: true
    context: |
      All CI runs on self-hosted runners that are occasionally evicted
      mid-build. Runner disconnects and image pull timeouts are always
      infrastructure.
    retriable_checks:
      - name: "e2e-*"
        context: |
          Also flaky when the seed database is still migrating.
      - name: "unit-tests"
```

Here the `e2e-*` checks are judged with both descriptions, and `unit-tests` with the repository-wide one alone.

### Keeping context in a file

For anything longer than a few lines, keep the prose in your repository instead. Files live under `.aviator/mergequeue/flake/` and must be Markdown:

```yaml
  flaky_retry:
    enabled: true
    context_file: ".aviator/mergequeue/flake/ci-overview.md"
    retriable_checks:
      - name: "e2e-*"
        context_file: ".aviator/mergequeue/flake/e2e.md"
```

`context` and `context_file` are mutually exclusive at each level — set one or the other. Aviator syncs these files from your default branch, so updating them is a normal pull request and the history of *why* a check is flaky lives next to the code.

An example `.aviator/mergequeue/flake/e2e.md`:

```markdown
# End-to-end suite

Runs against an ephemeral preview environment provisioned per build.

## Usually infrastructure

- `TimeoutError: waiting for selector` in the first test of a run. The
  environment is still warming up.
- `502 Bad Gateway` from `preview.internal`. The router occasionally drops
  the first request after a cold start.
- `ECONNREFUSED` against port 5432. Postgres has not finished migrating.

## Usually genuine

- Assertion failures comparing page content.
- `Element not found` for a selector that was recently renamed.
```

## What Aviator posts on the pull request

When a verdict changes what the queue does, Aviator leaves a comment so the decision is never silent. It is a standalone comment rather than a section of the status comment, which is rewritten in place as the queue advances and would lose the record.

Each comment names the checks it decided about, quotes the reasoning behind the verdict, and says how often that same failure has been seen in your repository before:

```markdown
### Flaky CI detected

**e2e-checkout** was rerun — the failure looks unrelated to this change.

> A timeout waiting for the preview environment to become ready, which this
> check's notes describe as infrastructure. The failure names no file this
> batch modifies.

Seen flaking on 7 other commits in this repository.

[Flake history](#) · [Failing job](#)
```

A failure Aviator has not seen before says **New failure** in place of the recurrence count. **Flake history** links to that specific failure on the flakiness dashboard, which is the quickest way in.

In [shadow mode](#shadow-mode) the heading reads **Flaky CI detected (shadow mode)**, and the check line reads "would have been treated as flaky; nothing was changed".

Aviator comments on every pull request in the batch as well as on the draft, since one failure holds all of them. In shadow mode it comments on the draft alone.

If a retry fails again, that is a failure: the pull request is dequeued as normal.

## Tuning alongside optimistic validation

Flaky test management works with [optimistic validation](../concepts/managing-flaky-tests-in-mergequeue.md), and the two interact through `optimistic_validation_failure_depth`.

```yaml
  merge_mode:
    type: "parallel"
  parallel_mode:
    use_optimistic_validation: true
    optimistic_validation_failure_depth: 2
  flaky_retry:
    enabled: true
    retriable_checks:
      - name: "e2e-*"
```

A failure judged transient does not count toward that depth, so the queue looks further ahead for it than for an ordinary failure. Your configured depth is a floor: a verdict can make the queue more patient, never less. You do not need to raise the depth to accommodate this feature. See [how Aviator handles a flaky failure](../concepts/flaky-test-management.md#how-aviator-handles-a-flaky-failure).

## Provider support

Available for **GitHub Actions** and **Buildkite**. The concept page covers this in more detail under [supported CI providers](../concepts/flaky-test-management.md#supported-ci-providers).

For GitHub Actions, Aviator retries the individual failing job rather than the whole workflow run, so matrix legs that already passed are not rerun. Jobs depending on the failed job via `needs:` are rerun with it.

For Buildkite, Aviator retries the failing step within the build.

Checks reporting as commit statuses rather than check runs can still be investigated, and can still stop a transient failure from dequeuing a pull request, but cannot be retried automatically.

## Related

* [Flaky test management](../concepts/flaky-test-management.md) — how a flake is identified, how it is handled, and the guarantees it holds to
* [Optimistic validation](../concepts/managing-flaky-tests-in-mergequeue.md) — waiting on later batches, which this builds on
* [Parallel mode](../concepts/parallel-mode/) — why a single failing check resets the queue
