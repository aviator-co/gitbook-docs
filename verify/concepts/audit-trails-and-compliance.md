# Audit trails and compliance

This document explains how Verify creates audit records and how they support compliance requirements.

### What gets recorded

Every significant action in Verify creates an audit record:

| Event               | What’s recorded                          |
| ------------------- | ---------------------------------------- |
| Spec created        | Author, timestamp, initial content       |
| Spec submitted      | Author, timestamp, reviewers requested   |
| Spec approved       | Approver, timestamp, final content       |
| Spec rejected       | Reviewer, timestamp, feedback            |
| Verification run    | Spec version, commit, timestamp, results |
| Verification passed | Full results, criteria checked           |
| Verification failed | Full results, failure details            |

Records are immutable. Once created, they cannot be modified or deleted.

### The audit chain

Each production change has a complete chain:

```
Intent Approved → Implementation Created → Verification Passed → Merged
     ↓                    ↓                       ↓               ↓
 Spec record         Commit SHA            Results record    Merge record
 (who, when,         (what code)           (what passed)    (final state)
  what intent)
```

This chain answers compliance questions:

* **What was intended?** → Spec record
* **Who approved it?** → Approval record
* **What was built?** → Commit reference
* **Was it verified?** → Verification record
* **What was checked?** → Results detail

### Segregation of duties

Compliance frameworks often require segregation of duties—the person who writes code shouldn’t be the only one who approves it.

Verify enforces this naturally:

| Role          | Actor             | Recorded |
| ------------- | ----------------- | -------- |
| Spec author   | Developer         | Yes      |
| Spec approver | Reviewer          | Yes      |
| Implementer   | Developer (or AI) | Yes      |
| Verifier      | Automated system  | Yes      |

The author and approver are tracked separately. If `allow_self_approval` is disabled (default), authors cannot approve their own specs.

This is stronger than traditional code review, where a reviewer might “approve” a PR they also committed to.

### Traceability

Every production change links back to:

* An approved spec (the intent)
* A verification result (the proof)
* External references (tickets, requirements)

If an auditor asks “why was this change made?”, you can show:

1. The spec that described the intent
2. Who approved that intent
3. Verification proving the implementation matches
4. The ticket or requirement that motivated it

### Compliance framework mapping

#### SOC 2

| Trust Service Criteria          | How Verify helps                            |
| ------------------------------- | ------------------------------------------- |
| CC6.1 - Logical access controls | Spec approval tracks who authorized changes |
| CC6.6 - Changes are authorized  | Every change requires approved spec         |
| CC6.7 - Changes are tested      | Verification checks every criterion         |
| CC8.1 - Change management       | Complete audit trail for all changes        |

#### ISO 27001

| Control                          | How Verify helps                           |
| -------------------------------- | ------------------------------------------ |
| A.12.1.2 - Change management     | Formal approval and verification process   |
| A.12.1.4 - Segregation of duties | Author ≠ approver, tracked separately      |
| A.14.2.2 - System change control | Audit trail links intent to implementation |

#### HIPAA

| Requirement        | How Verify helps                         |
| ------------------ | ---------------------------------------- |
| Access controls    | Only authorized users can approve specs  |
| Audit controls     | Immutable records of all changes         |
| Integrity controls | Verification ensures code matches intent |

### Generating compliance reports

#### On-demand export

1. Go to **Verify → Audit**
2. Filter by date range, repository, or author
3. Click **Export**
4. Choose format: JSON, CSV, or PDF

PDF reports are formatted for auditor review.

#### Scheduled reports

Configure automatic exports in **Verify → Settings → Scheduled Reports**:

* Weekly or monthly frequency
* Filtered by criteria
* Delivered via email or webhook

#### What’s included

Reports include:

* All spec approvals in the period
* All verification runs and results
* Actor information (who did what)
* Timestamps
* Result details

### Retention

Audit records are retained indefinitely by default.

For specific retention requirements, contact support to configure:

* Retention period
* Archival policies
* Data export before deletion

### Immutability

Audit records cannot be:

* Modified after creation
* Deleted by users
* Backdated

This ensures audit trails are trustworthy. What you see is what happened.

### What Verify doesn’t do

Verify provides audit trails for code changes. It doesn’t:

* Replace your ticketing system
* Provide runtime audit logs
* Track infrastructure changes
* Monitor production access

Integrate Verify with your other compliance tools for complete coverage.

### See also

* [How to export audit logs](../how-to-guides/export-audit-logs.md)
* [Reference: Configuration options](../reference/configuration-reference.md)
* [Why intent-driven verification](why-intent-driven-verification.md)
