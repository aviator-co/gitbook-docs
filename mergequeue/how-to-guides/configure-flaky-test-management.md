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
{% endhint %}

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

A check that is not listed is never investigated and never retried. Start with the one or two checks you know are flaky rather than listing everything.

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
A check that never fails deterministically is a poor candidate. Retrying a test that is genuinely broken wastes CI time and delays the feedback that it is broken. Use your flake rate in analytics to pick candidates.
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

When a check is retried, Aviator leaves a comment on the pull request explaining the decision, so the retry is never silent:

> **Aviator retried `e2e-checkout`**
>
> The failure on `a1b2c3d` looked transient, so the check was rerun instead of dequeuing this pull request.
>
> ```
> TimeoutError: waiting for selector `#confirm` after 30000ms
> ```
>
> **Why:** the failure names no file this change modifies, and matches a timeout waiting for the preview environment, which this check's notes describe as infrastructure.
>
> **History:** `e2e-checkout` has flaked 7 times in the last 90 days.
>
> The retry passed, and the pull request stayed in the queue.

If the retry fails again, the comment says so and the pull request is dequeued as normal.

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

## Rolling it out

1. Turn it on for one repository with one known-flaky check.
2. Leave it for a week and read the comments Aviator posts. They tell you what it concluded and why.
3. Refine the context based on any decision you disagree with. Most corrections are a sentence naming a failure mode as genuine.
4. Add further checks once the first is behaving.

## Provider support

Available for **GitHub Actions** and **Buildkite**. The concept page covers this in more detail under [supported CI providers](../concepts/flaky-test-management.md#supported-ci-providers).

For GitHub Actions, Aviator retries the individual failing job rather than the whole workflow run, so matrix legs that already passed are not rerun. Jobs depending on the failed job via `needs:` are rerun with it.

For Buildkite, Aviator retries the failing step within the build.

Checks reporting as commit statuses rather than check runs can still be investigated, and can still stop a transient failure from dequeuing a pull request, but cannot be retried automatically.

## Related

* [Flaky test management](../concepts/flaky-test-management.md) — how a flake is identified, how it is handled, and the guarantees it holds to
* [Managing flaky tests](../concepts/managing-flaky-tests-in-mergequeue.md) — optimistic validation, which this builds on
* [Parallel mode](../concepts/parallel-mode/) — why a single failing check resets the queue
