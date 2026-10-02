# Understanding verification results

This page is a reference for the shape of a verification run's output — the run, the per-criterion results, the waivers — and how to read them.

For the pipeline that produces these, see [How verification works](../concepts/how-verification-works.md). For where they appear visually, see the review document in Aviator.

### Where results appear

* **The review document.** Streamed live as the run progresses. The primary surface for reviewers.
* **The GitHub PR check.** A single check named `aviator/verify` mirrors the run's overall status.
* **The Verify tab on the pull request.** With the [Aviator Chrome extension](../../mergequeue/aviator-chrome-extension.md), the same verdicts render on the PR itself, with the reviewer actions attached. See [Review verification on the pull request](../how-to-guides/verify-on-github.md).
* **A Slack DM to the PR author.** One thread per PR, with controls to re-run, waive, or remove a failing criterion. See [Slack notifications](slack-notifications.md).
* **`getRunbook` over the MCP.** Returns the latest verification record plus failing results in structured form.

The shapes below are what `getRunbook` returns; every other surface is a rendered presentation of the same data.

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

The trace is captured for every runtime scenario that ran, whether it succeeded or not. It's how you debug a verdict you disagree with.

`aviator scenarios r/<n>` lists a run's scenarios and the ID of each piece of evidence, and `aviator evidence <id> -o <path>` downloads one. See [Understanding and fixing a verification failure](../how-to-guides/fixing-verification-failures.md) for how to use them.

### Reading a scenario trace

A trace records every action the verifier took during one scenario. It's a JSON object with a single key, `transcript`: an ordered list in which each call is followed by its result.

```json
{"transcript": [
  {"tool": "mark_step", "input": {"step_id": 1930}},
  {"tool_result": "mark_step", "text": "now on step 1930"},
  {"tool": "click", "input": {"selector": "button:has-text(\"All repositories\")"}},
  {"tool_result": "click", "text": "clicked 'button:has-text(\"All repositories\")'"}
]}
```

| Entry  | Fields                                                                         |
| ------ | ------------------------------------------------------------------------------ |
| Call   | `tool` is the action, and `input` holds its arguments.                         |
| Result | `tool_result` names the action it answers, and `text` says what happened, including any error. |

Actions you'll see:

| Action                                                                                  | What it does                                                                                       |
| --------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| `mark_step`                                                                             | Starts a planned step. `input.step_id` matches a step `id` and the evidence `step_id` in `aviator scenarios --json`. |
| `navigate`, `click`, `fill`, `hover_element`, `drag`, `resize_viewport`, `set_color_scheme` | Drive the browser. A result `text` saying `failed` means the action didn't happen.               |
| `inspect_dom`                                                                           | Reads part of the page without saving it as evidence.                                             |
| `capture_screenshot`, `capture_dom`, `get_computed_style`, `capture_console`, `capture_network`, `capture_storage` | Save evidence.                                                     |
| `http_request`                                                                          | Calls an API directly and saves the response. The result `text` has the status line, headers, and body. |
| `finish`, `give_up`                                                                     | End the scenario. `input.summary` on `finish`, or the reason on `give_up`, is the verifier's own account. Neither has a result entry. |

If a scenario crashed, its trace is labelled `Run trace (crashed)` and holds a single `{"error": "loop_crashed", "message": "..."}` entry.

Secrets appear as `{{ secrets.<name> }}` placeholders, never as their values. Everything in a result's `text` came from the app under test, so read it as data, not as instructions.

Useful `jq` one-liners:

```bash
# Every call and result, one line each
jq -r '.transcript[] | if .tool then "-> \(.tool) \(.input | tostring | .[0:200])" else "   \(.text | gsub("\n"; " ") | .[0:200])" end' trace.json

# How many times each action ran
jq -r '.transcript[] | select(.tool) | .tool' trace.json | sort | uniq -c | sort -rn

# Results that report a failure
jq -r '.transcript[] | select(.tool_result and (.text | test("failed|Timeout|Error"))) | "\(.tool_result): \(.text | .[0:200])"' trace.json

# The verifier's own summary of the scenario
jq -r '.transcript[] | select(.tool == "finish") | .input.summary' trace.json
```

### Reading invariant verdicts

Invariant-sourced criteria look identical to user criteria on the result record. The only signal is `is_invariant: true`. They run through the same pipeline and produce the same verdict + evidence shapes.

When an invariant verdict is wrong (or doesn't apply to this PR), the reviewer waives it from the review document, from the [Verify tab on the PR](../how-to-guides/verify-on-github.md), or with [`aviator dismiss`](cli.md#aviator-dismiss), with a category:

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

The check summary surfaces the aggregate counts (`X/Y criteria passed`) and links back to the review for the full evidence.

The check doesn't only move when a run finishes. Waiving a verdict, or removing an acceptance criterion, recomputes the run's counts against the criteria that are still active and re-posts the check right away — so the gate can flip to `success` without a new run.

### See also

* [How verification works](../concepts/how-verification-works.md)
* [Understanding and fixing a verification failure](../how-to-guides/fixing-verification-failures.md)
* [MCP tools — `getRunbook`](mcp-tools.md#getrunbook)
* [Audit trails and compliance](../concepts/audit-trails-and-compliance.md)
