# Audit trails and compliance

Every Verify run produces an immutable record of what was submitted, what ran, what was found, and what was decided. This page covers what's recorded, how it composes into a chain you can hand an auditor, and how Verify maps to common compliance frameworks.

### What gets recorded

| Event                | What's recorded                                                          |
| -------------------- | ------------------------------------------------------------------------ |
| Intent submitted     | Submitter, timestamp, intent text, acceptance criteria, repo + commit    |
| Verification started | Run ID, preview reference, set of matching invariants                    |
| Verdict produced     | Criterion or invariant, verifier method, verdict, evidence reference     |
| Reviewer decision    | Approver, timestamp, action (approve, send back, waive), reason if waived |
| Preview event        | Boot, ready, scenario run, teardown — each with timestamp and exit state |

Records are immutable. Once an event is written, it can't be modified or deleted by users.

### The audit chain

Every merged change has a complete chain:

```
Implementation → Intent submitted → Verification run → Reviewer decision → Merged
       ↓                ↓                  ↓                   ↓             ↓
   Commit SHA      Submission         Verdict +            Approval      Merge
                    record            evidence per         record       reference
                                      criterion
```

This chain answers the questions an auditor asks:

* **What was the change supposed to do?** → Intent + criteria from the submission record.
* **Did the running code actually do it?** → Verdicts with evidence from the verification record.
* **Who decided this could merge?** → Reviewer decision record.
* **What was overridden, and why?** → Waiver records with reasons.

### Segregation of duties

Compliance frameworks often require segregation — the person making a change shouldn't be the only one who signs off on it.

Verify supports this naturally:

| Role           | Actor               | Tracked separately |
| -------------- | ------------------- | ------------------ |
| Submitter      | The developer (or remote agent caller) calling the MCP | yes |
| Reviewer       | The person approving from the review document           | yes |
| Verifier       | Automated — the Verify pipeline itself                  | yes |

Org policy can require that the reviewer differ from the submitter. The audit chain makes the separation explicit: a single change can't merge without two distinct actors appearing on it.

This is stronger than diff review, where the same person can leave a comment and merge their own PR.

### Traceability

Every production change links back to:

* The submitted intent (the *what was supposed to happen*).
* The verifier verdicts and evidence (the *what actually happened*).
* The reviewer decision (the *who said yes*).
* The commit SHA (the *what shipped*).

If an auditor asks "why was this change made and how do you know it was safe," you can show all four in one query.

### Compliance framework mapping

#### SOC 2

| Trust Service Criteria          | How Verify helps                                                       |
| ------------------------------- | ---------------------------------------------------------------------- |
| CC6.1 — Logical access controls | Reviewer decisions track who authorized each change.                   |
| CC6.6 — Authorized changes      | Every merged change has a reviewer record.                             |
| CC6.7 — Changes are tested      | Verification produces a verdict for every criterion and invariant.     |
| CC8.1 — Change management       | Complete chain: intent → verification → review → merge, immutable.     |

#### ISO 27001

| Control                          | How Verify helps                                                       |
| -------------------------------- | ---------------------------------------------------------------------- |
| A.12.1.2 — Change management     | Structured submission + review process for every code change.          |
| A.12.1.4 — Segregation of duties | Submitter ≠ reviewer, enforceable via org policy.                      |
| A.14.2.2 — System change control | Audit chain links intent to implementation to verdict.                 |

#### HIPAA

| Requirement        | How Verify helps                                                            |
| ------------------ | --------------------------------------------------------------------------- |
| Access controls    | Only authorized reviewers can approve; access is logged.                    |
| Audit controls     | Immutable records of every submission, verdict, and reviewer decision.      |
| Integrity controls | Verification gives evidence that the code matches the declared intent.      |

### Exporting reports

The audit trail is queryable and exportable from the Aviator UI. See [How to export audit logs](../how-to-guides/export-audit-logs.md) for the current export surface.

### Retention

Audit records are retained indefinitely by default. Contact support if you have a regulatory requirement for a different retention or archival policy.

### Immutability

Audit records cannot be:

* Modified after creation.
* Deleted by users.
* Backdated.

The trail is what happened, not a summary someone wrote afterwards.

### What Verify doesn't do

Verify provides an audit trail for code changes. It doesn't:

* Replace your ticketing or change-management system.
* Provide runtime audit logs (request-level access, API call audits).
* Track infrastructure or configuration changes outside source control.
* Monitor production access or data handling.

Integrate Verify alongside your other compliance tools for full coverage.

### See also

* [How to export audit logs](../how-to-guides/export-audit-logs.md)
* [How verification works](how-verification-works.md)
* [Why intent-driven verification](why-intent-driven-verification.md)
