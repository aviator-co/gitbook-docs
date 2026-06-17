# Export audit logs

Export verification and reviewer-decision records from the Aviator UI for compliance reporting.

For the underlying concept and what each record contains, see [Concepts: Audit trails and compliance](../concepts/audit-trails-and-compliance.md).

### Prerequisites

* Admin access to your Aviator organization.
* At least one verification run in the period you want to export.

### Exporting from the dashboard

#### 1. Open the audit view

Go to **Verify → Audit**.

You'll see a chronological list of events: intent submissions, verification runs, verdicts, reviewer decisions, and waivers.

#### 2. Filter the data

Use the filters at the top to narrow:

| Filter        | Options                                              |
| ------------- | ---------------------------------------------------- |
| Date range    | Last 7 days, 30 days, 90 days, or custom range.      |
| Repository    | All repos, or a specific repo.                       |
| Event type    | Submission, verdict, decision, waiver.               |
| Actor         | All users, or a specific submitter or reviewer.      |
| Outcome       | Passed, failed, waived.                              |

#### 3. Export

Click **Export** and choose a format:

* **JSON** — machine-readable, good for downstream processing.
* **CSV** — spreadsheet-compatible.
* **PDF** — formatted report, good for handing to auditors directly.

The export contains every event matching your filters.

### What an exported record looks like

Each record carries the same shape:

| Field             | Description                                                              |
| ----------------- | ------------------------------------------------------------------------ |
| `timestamp`       | When the event occurred (ISO 8601).                                       |
| `event_type`      | `intent_submitted`, `verdict_produced`, `reviewer_decision`, `waiver`, `preview_event`. |
| `actor`           | Who performed the action — submitter, reviewer, or `system` for automated events. |
| `repo`, `branch`  | Git context.                                                             |
| `commit_sha`      | The commit the event references.                                          |
| `run_id`          | The verification run the event belongs to.                                |
| `payload`         | Event-specific fields (the verdict, the criterion text, the waiver reason, etc.). |

### Generating compliance evidence

For an audit period, the most useful exports:

* **Submissions + decisions.** Demonstrates that every change had an explicit intent and a reviewer sign-off.
* **Verdicts.** Demonstrates that every change was systematically verified against its criteria and against the team's invariants.
* **Waivers.** Demonstrates that exceptions were explicit, attributed, and reasoned.

Filter to your audit period and export as PDF for sharing. See [Concepts: Audit trails and compliance — Compliance framework mapping](../concepts/audit-trails-and-compliance.md#compliance-framework-mapping) for SOC 2 / ISO 27001 / HIPAA references.

### Retention

Audit records are retained indefinitely by default. If you need a specific retention or archival policy, contact support.

### See also

* [Audit trails and compliance](../concepts/audit-trails-and-compliance.md)
* [Configuration reference](../reference/configuration-reference.md)
