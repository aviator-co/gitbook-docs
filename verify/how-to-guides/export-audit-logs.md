# Export audit logs

Export verification and approval records for compliance reporting.

### Prerequisites

* Admin access to your Aviator organization
* Completed verifications you want to export

### Exporting from the dashboard

#### 1. Navigate to audit logs

Go to **Verify → Audit**.

You’ll see a chronological list of all verification events: spec creations, approvals, verification runs, and results.

#### 2. Filter the data

Use filters to narrow down what you need:

| Filter         | Options                                                               |
| -------------- | --------------------------------------------------------------------- |
| **Date range** | Last 7 days, 30 days, 90 days, custom                                 |
| **Repository** | All or specific repository                                            |
| **Event type** | Spec created, Spec approved, Verification passed, Verification failed |
| **Author**     | All or specific user                                                  |

#### 3. Export

Click **Export** and choose a format:

* **JSON** — Machine-readable, good for processing
* **CSV** — Spreadsheet-compatible
* **PDF** — Formatted report, good for auditors

The export includes all events matching your filters.

### What’s included in exports

Each audit record contains:

| Field             | Description                                                               |
| ----------------- | ------------------------------------------------------------------------- |
| `timestamp`       | When the event occurred                                                   |
| `event_type`      | Type of event (spec\_created, spec\_approved, verification\_passed, etc.) |
| `actor`           | Who performed the action                                                  |
| `spec_id`         | The spec involved                                                         |
| `spec_title`      | Human-readable spec title                                                 |
| `repository`      | Repository name                                                           |
| `pr_number`       | Associated pull request (if applicable)                                   |
| `commit_sha`      | Commit that was verified (if applicable)                                  |
| `verification_id` | Unique ID for verification runs                                           |
| `result`          | Pass/fail details                                                         |

### Generating SOC 2 evidence

For SOC 2 audits, export:

1. **Spec approvals** — Shows segregation of duties (author ≠ approver)
2. **Verification results** — Shows systematic verification of all changes
3. **Failure logs** — Shows failures were addressed before merge

Filter by date range matching your audit period, export as PDF for easy sharing.

### Scheduled exports

To schedule automatic exports:

1. Go to **Verify → Settings → Scheduled Reports**
2. Click **Add Schedule**
3. Configure:
   * Frequency (weekly, monthly)
   * Filters (repository, event types)
   * Format (JSON, CSV, PDF)
   * Delivery (email, webhook)

Reports are generated and delivered automatically.

### API export

For programmatic access, use the Audit API:

```bash
curl -X GET "<https://api.aviator.co/v1/verify/audit>" \\
  -H "Authorization: Bearer$AVIATOR_API_KEY" \\
  -d "start_date=2024-01-01" \\
  -d "end_date=2024-01-31" \\
  -d "repository=your-org/your-repo"
```

See API Reference: Audit for full documentation.

### Retention

Audit logs are retained indefinitely by default. Contact support if you need a specific retention policy.

### See also

* Explanation: Audit trails and compliance
* Reference: Configuration options
