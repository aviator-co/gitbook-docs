# Understanding verification results

Reference for understanding verification output.

### Result locations

Verification results appear in:

* GitHub PR check (summary)
* Aviator dashboard (full details)
* Slack/email notifications (if configured)

### Overall status

| Status    | Meaning                           |
| --------- | --------------------------------- |
| `passed`  | All checks passed                 |
| `failed`  | One or more checks failed         |
| `running` | Verification in progress          |
| `error`   | Verification encountered an error |
| `pending` | Verification queued               |

### Result structure

```json
{
  "verification_id": "ver_abc123",
  "status": "failed",
  "spec": {
    "id": "spec_xyz789",
    "title": "Add subscription status endpoint",
    "version": 1
  },
  "repository": "your-org/your-repo",
  "pr_number": 234,
  "commit_sha": "a1b2c3d4",
  "results": {
    "scope": { ... },
    "acceptance_criteria": { ... },
    "org_invariants": { ... }
  },
  "audit_trail_id": "aud_def456",
  "started_at": "2024-01-28T16:00:00Z",
  "completed_at": "2024-01-28T16:01:23Z"
}
```

### Scope results

```json
{
  "scope": {
    "status": "passed",
    "modified_files": [
      "src/handlers/subscription.go",
      "src/models/subscription.go"
    ],
    "declared_files": [
      "src/handlers/subscription.go",
      "src/models/subscription.go"
    ],
    "violations": []
  }
}
```

When failed:

```json
{
  "scope": {
    "status": "failed",
    "modified_files": [
      "src/handlers/subscription.go",
      "src/auth/middleware.go"
    ],
    "declared_files": [
      "src/handlers/subscription.go"
    ],
    "violations": [
      {
        "type": "undeclared_modification",
        "file": "src/auth/middleware.go",
        "message": "Modified file not in declared scope"
      }
    ]
  }
}
```

#### Violation types

| Type                      | Description                              |
| ------------------------- | ---------------------------------------- |
| `undeclared_modification` | Changed a file not in modify list        |
| `forbidden_modification`  | Changed a file matching a forbid pattern |
| `undeclared_service_call` | Called a service not in call list        |

### Acceptance criteria results

```json
{
  "acceptance_criteria": {
    "status": "failed",
    "total": 7,
    "passed": 5,
    "failed": 2,
    "criteria": [
      {
        "criterion": "Endpoint: GET /api/v1/subscription/status",
        "status": "passed"
      },
      {
        "criterion": "Response excludes: internal_id, billing_provider_id",
        "status": "failed",
        "reason": "Response includes billing_provider_id",
        "location": {
          "file": "src/models/subscription.go",
          "line": 24
        },
        "code_snippet": "BillingID string `json:\\"billing_provider_id\\"`",
        "suggested_fix": "Remove billing_provider_id from response struct"
      }
    ]
  }
}
```

#### Criterion statuses

| Status         | Meaning                            |
| -------------- | ---------------------------------- |
| `passed`       | Implementation satisfies criterion |
| `failed`       | Implementation violates criterion  |
| `inconclusive` | Could not determine (rare)         |

### Org invariants results

```json
{
  "org_invariants": {
    "status": "passed",
    "total": 12,
    "passed": 12,
    "failed": 0,
    "invariants": [
      {
        "name": "security-baseline",
        "status": "passed",
        "rules": [
          {
            "rule": "All HTTP handlers must use AuthMiddleware",
            "status": "passed"
          },
          {
            "rule": "No hardcoded credentials",
            "status": "passed"
          }
        ]
      }
    ]
  }
}
```

### GitHub check output

Summary shown on PR:

```
✓ Aviator Verify — All checks passed
  7/7 criteria passed · 12 org invariants passed
```

Or when failed:

```
✗ Aviator Verify — 2 checks failed
  5/7 criteria passed · 1 scope violation
```

### Error states

When verification fails to run (not a code failure):

```json
{
  "status": "error",
  "error": {
    "type": "timeout",
    "message": "Verification timed out after 300 seconds"
  }
}
```

| Error type       | Cause                        |
| ---------------- | ---------------------------- |
| `timeout`        | Verification took too long   |
| `parse_error`    | Could not parse spec or code |
| `internal_error` | Aviator system error         |

### See also

* [How to fix failures](../how-to-guides/fixing-verification-failures.md)
* [How verification works](../concepts/how-verification-works.md)
