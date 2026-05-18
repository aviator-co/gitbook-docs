# Understanding verification results

Reference for the output produced by a verification run.

### Where results appear

* **GitHub PR check** — `aviator/verify`, with a markdown summary
* **Aviator dashboard** — the runbook's verify page, with full per-criterion detail

### Run status

Each verification run has one of these statuses:

| Status        | Meaning                                                            |
| ------------- | ------------------------------------------------------------------ |
| `PENDING`     | Run created, not yet started                                       |
| `IN_PROGRESS` | Run is executing                                                   |
| `PASSED`      | All criteria passed                                                |
| `FAILED`      | At least one criterion failed                                      |
| `ERROR`       | The run could not complete (e.g., agent error, missing prerequisite) |
| `DEFERRED`    | Waiting on baseline-invariant selection to complete                |

### Per-criterion result

Each criterion in the run produces one result:

| Status   | Meaning                                              |
| -------- | ---------------------------------------------------- |
| `PASS`   | Verifier judged the implementation satisfies it      |
| `FAIL`   | Verifier judged the implementation does not satisfy it |
| `ERROR`  | The verifier could not produce a verdict             |

A `FAIL` includes:

* A reason — the verifier's explanation
* A location — file and line (when the verifier could identify them)
* A short evidence snippet — the relevant code or context

### GitHub check output

Passing run:

```
✓ aviator/verify — All criteria passed
  7/7 criteria passed
```

Failing run:

```
✗ aviator/verify — 2 criteria failed
  5/7 criteria passed
```

The full check body is a markdown table with one row per criterion: status, criterion text, reason for failure. The check links back to the runbook's verify page.

### Dashboard view

The runbook's verify page shows:

* Overall run status and timing
* The full criteria set (spec criteria + applicable invariants)
* Per-criterion verdict, reason, evidence, and location
* Links to the commit and PR

### What is not currently exposed

Several output fields that compliance-leaning customers may expect are not yet implemented:

* Content-addressed evidence — verifier output is text, not anchored to specific captured artifacts
* Re-runnable verdicts — there is no replay endpoint that re-evaluates a past run against new criteria
* Structured export — JSON/CSV/PDF export is on the roadmap

These are tracked as part of the audit-and-replay workstream.

### Error states

When a run cannot complete:

| Cause                 | Run status | Notes                                                |
| --------------------- | ---------- | ---------------------------------------------------- |
| Agent error           | `ERROR`    | LLM call failed or returned unparseable output       |
| Missing prerequisite  | `DEFERRED` | Baseline-invariant selector still running for the SHA |
| Stale commit          | `ERROR`    | A newer commit superseded this one mid-run           |

### See also

* [How verification works](../concepts/how-verification-works.md)
* [How to fix failures](../how-to-guides/fixing-verification-failures.md)
