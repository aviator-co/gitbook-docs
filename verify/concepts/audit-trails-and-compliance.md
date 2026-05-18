# Records of what was checked

This document explains what Verify records when it runs and how those records are surfaced today.

Verify is in active development. Compliance-grade audit export (SOC 2, ISO 27001, HIPAA) is on the roadmap but not yet implemented. The records described here are useful for reviewing what happened on a given PR; they are not yet structured for auditor consumption.

### What gets recorded

Each verification run records:

| Field                | Description                                                  |
| -------------------- | ------------------------------------------------------------ |
| Spec applied         | Reference to the spec used                                   |
| Acceptance criteria  | The criteria evaluated, including any from invariants        |
| Verdict per criterion | Pass, fail, or error                                         |
| Reason / evidence    | Verifier's text reason, file/line where applicable           |
| Commit SHA           | The commit verified                                          |
| PR reference         | Linked pull request                                          |
| Started / completed  | Timestamps                                                   |

These records are stored as `VerificationRun` and `VerificationResult` rows. They are visible:

* In the dashboard, on the runbook's verify page
* In the GitHub check summary

### What is not recorded today

The following are commonly expected of compliance-grade systems and are not yet implemented:

* **Immutable, signed audit chain.** Records are stored in the application database. They are not yet content-addressed or cryptographically sealed.
* **Segregation-of-duties enforcement.** The system does not enforce that author ≠ approver. Authors today can approve their own criteria.
* **Exportable reports.** There is no built-in export to JSON, CSV, or PDF, and no scheduled report generation.
* **Compliance framework mappings.** Verify does not currently produce evidence formatted for SOC 2, ISO 27001, or HIPAA audits.

These are roadmap items. If audit-grade output is required for your team, this is a feature area where design-partner input shapes our priorities.

### What you can do today

For each verification run, the dashboard and GitHub check show:

* Which criteria applied
* What the verdict for each was
* The verifier's reason and any cited location

For a given PR, this gives reviewers and authors a clearer record than a comment thread — the criteria are explicit, the verdicts are recorded, and the evidence is cited.

For repo-wide review, the dashboard at `/r/...` shows the runbook history including verification runs. There is no aggregated cross-repo or date-range view today.

### Retention

Verification records are retained as long as the underlying database retains them. Retention policies, archival, and selective deletion are not currently configurable.

### See also

* [How verification works](how-verification-works.md)
* [Understanding verification results](../reference/understanding-verification-results.md)
