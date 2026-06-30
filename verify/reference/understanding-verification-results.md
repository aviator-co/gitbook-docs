# Understanding verification results

This page is a reference for the shape of a verification run's output — the run, the per-criterion results, the waivers — and how to read them.

For the pipeline that produces these, see [How verification works](../concepts/how-verification-works.md). For where they appear visually, see the runbook review document in the Aviator UI.

### Where results appear

* **The runbook's review document.** Streamed live as the run progresses. The primary surface for reviewers.
* **The GitHub PR check.** A single check named `aviator/verify` mirrors the run's overall status.
* **`getRunbook` over the MCP.** Returns the latest verification record plus failing results in structured form.

The shapes below are what `getRunbook` returns; the UI and PR check are rendered presentations of the same data.

### Run-level status

| Status         | Meaning                                                                |
| -------------- | ---------------------------------------------------------------------- |
| `pending`      | Queued, hasn't started yet.                                            |
| `in_progress`  | Currently executing.                                                   |
| `passed`       | Every criterion passed (or was waived).                                |
| `failed`       | At least one criterion failed without a waiver.                        |
| `error`        | The run itself errored — pipeline issue, not a code verdict.            |
| `deferred`     | Waiting for baseline-invariant selection to finish before it can start. |

### Run record shape

A run's record carries trigger context plus aggregate counts:

| Field            | Description                                                                            |
| ---------------- | -------------------------------------------------------------------------------------- |
| `status`         | One of the values above.                                                               |
| `trigger_source` | What kicked the run off: `manual`, `ready`, `approval`, `queued`, `linked`, `criteria_edit`. |
| `runbook_version`| Version of the runbook that was verified (matches what `getRunbook` returned at submission time). |
| `commit_sha`     | The commit verified.                                                                   |
| `criteria_total` | Total criteria evaluated in this run.                                                   |
| `criteria_passed` | Passed.                                                                                |
| `criteria_failed` | Failed (excluding waived).                                                             |
| `criteria_skipped` | Could not be evaluated (e.g. preview boot failed).                                    |
| `criteria_waived` | Failed but explicitly waived by a reviewer.                                            |
| `error_message`  | Only set when `status = error`.                                                         |

### Per-criterion result shape

Each criterion produces one result:

| Field         | Description                                                                                  |
| ------------- | -------------------------------------------------------------------------------------------- |
| `criterion`   | The criterion text.                                                                          |
| `is_invariant` | True if the criterion was materialized from an [invariant](../concepts/invariants.md) (source: `baseline_invariant`). |
| `is_waived`   | True if a reviewer has waived this verdict.                                                  |
| `status`      | `pass`, `fail`, `warn`, or `error` (see below).                                              |
| `evidence`    | Structured reference to the captured artifact backing the verdict.                           |
| `reason`      | Verifier-produced explanation when present.                                                  |
| `location`    | File + line range when the verifier could attribute one.                                     |

### Criterion-level status

| Status    | Meaning                                                                                                |
| --------- | ------------------------------------------------------------------------------------------------------ |
| `pass`    | Implementation satisfies the criterion.                                                                |
| `fail`    | Implementation violates the criterion (or the verifier couldn't confirm it should pass).               |
| `warn`    | Concerning but not failing — the verifier flagged something the reviewer should look at without blocking the merge gate. |
| `error`   | The verifier itself threw an exception or returned an inconclusive result. Treat as needing human review. |

### Evidence by verifier path

The shape of `evidence` depends on which verifier produced the verdict:

| Verifier path | Evidence shape                                                            |
| ------------- | ------------------------------------------------------------------------- |
| Code-scan     | File + line range plus the relevant code snippet (diff or AST excerpt).   |
| Runtime       | One or more of: `screenshot`, `console_log`, `dom_snapshot`, `api_response`, plus a `trace` (full agent transcript for the scenario run). |

The trace is captured for every runtime scenario regardless of success — it's how you debug a verdict you disagree with.

### Reading invariant verdicts

Invariant-sourced criteria look identical to user criteria on the result record. The only signal is `is_invariant: true`. They run through the same pipeline and produce the same verdict + evidence shapes.

When an invariant verdict is wrong (or doesn't apply to this PR), the reviewer waives it from the review document with a category:

| Waiver category    | When to use it                                                                 |
| ------------------ | ------------------------------------------------------------------------------ |
| `false_positive`   | The invariant fired but the rule misjudged this case.                          |
| `doesnt_apply`     | The rule is valid in general but isn't relevant to this PR.                    |
| `accepted_risk`    | The failure is real but the PR author accepts the trade-off.                   |
| `fix_in_followup`  | The failure is real and will be addressed in a separate follow-up PR.          |

Every waiver is recorded with the reviewer, the category, and a free-text reason. See [Audit trails and compliance](../concepts/audit-trails-and-compliance.md).

### Reading the PR check

The PR check named `aviator/verify` mirrors the run's overall status:

| Run status    | GitHub check state                              |
| ------------- | ----------------------------------------------- |
| `pending`     | `queued`                                        |
| `in_progress` | `in_progress`                                   |
| `passed`      | `success`                                       |
| `failed`      | `failure`                                       |
| `error`       | `failure`                                       |
| `deferred`    | `in_progress` until the run actually starts.    |

The check summary surfaces the aggregate counts (`X/Y criteria passed`) and links back to the runbook for the full evidence.

### See also

* [How verification works](../concepts/how-verification-works.md)
* [Fixing verification failures](../how-to-guides/fixing-verification-failures.md)
* [MCP tools — `getRunbook`](mcp-tools.md#getrunbook)
* [Audit trails and compliance](../concepts/audit-trails-and-compliance.md)
