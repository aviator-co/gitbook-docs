# Export audit logs

Export verification and reviewer-decision records from the Aviator UI for compliance reporting.

For the underlying concept and what each record contains, see [Concepts: Audit trails and compliance](../concepts/audit-trails-and-compliance.md).

### Prerequisites

* Admin access to your Aviator organization.
* At least one verification run in the period you want to export.

### Exporting from the dashboard

#### 1. Open the audit view

Go to **Verify → Audit**.

You'll see a chronological list of records: runbook submissions, verification runs, individual verdicts, and reviewer actions (approvals + waivers).

#### 2. Filter the data

Use the filters at the top to narrow:

| Filter         | Options                                                                      |
| -------------- | ---------------------------------------------------------------------------- |
| Date range     | Last 7 days, 30 days, 90 days, or custom range.                              |
| Repository     | All repos, or a specific repo.                                               |
| Record type    | Runbook submissions, verification runs, verdicts, waivers.                   |
| Actor          | All users, or a specific submitter or reviewer.                              |
| Verdict status | `pass`, `fail`, `warn`, `error`.                                              |
| Waiver category| `false_positive`, `doesnt_apply`, `accepted_risk`, `fix_in_followup`.        |

#### 3. Export

Click **Export** and choose a format:

* **JSON** — machine-readable, good for downstream processing.
* **CSV** — spreadsheet-compatible.
* **PDF** — formatted report, good for handing to auditors directly.

The export contains every record matching your filters.

### Record shapes

Each record carries fields specific to its type. Common shapes:

**Runbook submission:**

| Field           | Description                                                        |
| --------------- | ------------------------------------------------------------------ |
| `runbook_number`| User-facing runbook ID.                                            |
| `submitted_by`  | Submitter (the user the MCP token belonged to, or the UI user).    |
| `submitted_at`  | ISO 8601 timestamp.                                                |
| `repo`, `branch`| Git context — target branch and working branch.                    |
| `commit_sha`    | The HEAD commit at submission time.                                |
| `message`       | The submission message.                                            |
| `spec_files`    | If submitted with spec files: each filename + content.             |

**Verification run:**

| Field           | Description                                                                       |
| --------------- | --------------------------------------------------------------------------------- |
| `run_id`        | Unique ID.                                                                        |
| `runbook_number`| The runbook this run belongs to.                                                  |
| `runbook_version`| The version of the runbook that was verified.                                    |
| `trigger_source`| `manual`, `commit_push`, `step_complete`, or `criteria_edit`.                     |
| `commit_sha`    | The commit verified.                                                              |
| `status`        | `pending`, `in_progress`, `passed`, `failed`, `error`, `deferred`.                |
| `criteria_total / passed / failed / skipped / waived` | Aggregate counts.                            |
| `started_at`, `completed_at` | ISO 8601 timestamps.                                                |

**Verification result (one per criterion):**

| Field          | Description                                                              |
| -------------- | ------------------------------------------------------------------------ |
| `run_id`       | The run this result belongs to.                                          |
| `criterion`    | The criterion text.                                                      |
| `is_invariant` | True if the criterion was materialized from an invariant.                |
| `is_waived`    | True if the verdict has been waived.                                     |
| `status`       | `pass`, `fail`, `warn`, `error`.                                          |
| `evidence`     | Reference to the captured evidence (snippet, screenshot, API response).  |
| `reason`       | Verifier-produced explanation.                                           |
| `location`     | File + line range when applicable.                                       |

**Waiver:**

| Field         | Description                                                       |
| ------------- | ----------------------------------------------------------------- |
| `result_id`   | The verification result being waived.                             |
| `waived_by`   | Reviewer.                                                         |
| `waived_at`   | ISO 8601 timestamp.                                               |
| `category`    | `false_positive`, `doesnt_apply`, `accepted_risk`, `fix_in_followup`. |
| `reason`      | Free-text justification.                                          |

### Generating compliance evidence

For an audit period, the most useful exports:

* **Runbook submissions + reviewer decisions.** Demonstrates that every change had an explicit submission and a recorded sign-off.
* **Verification results.** Demonstrates that every change was systematically verified against its criteria and against the team's invariants.
* **Waivers.** Demonstrates that exceptions were categorized, attributed, and reasoned.

Filter to your audit period and export as PDF for sharing. See [Concepts: Audit trails and compliance — Compliance framework mapping](../concepts/audit-trails-and-compliance.md#compliance-framework-mapping) for SOC 2 / ISO 27001 / HIPAA references.

### Retention

Audit records are retained indefinitely by default. If you need a specific retention or archival policy, contact support.

### See also

* [Audit trails and compliance](../concepts/audit-trails-and-compliance.md)
* [Configuration reference](../reference/configuration-reference.md)
