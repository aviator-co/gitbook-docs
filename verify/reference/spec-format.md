# Spec format

This page documents the shape of what the Aviator MCP submits when your agent calls `submit-spec`. The agent generates this content — you don't author it by hand — but the format is documented here so you can read submissions, debug, or build tools against the stored form.

For the conceptual flow, see [How Verify works](../how-it-works.md). For the MCP tool itself, see [MCP tools](mcp-tools.md).

### Structure

Every submission has a title and three sections:

```markdown
# Title

## Intent
[plain-language description]

## Scope
[files the change touches]

## Acceptance Criteria
[checklist of verifiable assertions]
```

There is no separate "execution steps" or "implementation plan" section. The agent has already implemented the change by the time it submits — there's nothing to plan.

### Title

A short description of the change. Appears in the dashboard, PR check name, and audit trail.

```markdown
# Add per-user rate limiting to public API
```

Keep it under ~80 characters. The title is what reviewers see first in the inbox.

### Intent

Plain-language description of *what* this change is for and *why*. Captures the constraints that aren't visible from the diff alone.

```markdown
## Intent
Cap per-user request rate on /api/v1/public/* endpoints. On overflow,
return 429 with a Retry-After header so well-behaved clients can back off.
Existing internal endpoints are unaffected.
```

The intent is the contract reviewers approve against. It doesn't describe implementation choices — those live in the code itself.

### Scope

The files the change touched, as observed by the agent at submission time.

```markdown
## Scope
- src/handlers/rate_limit.go
- src/middleware/chain.go
- tests/middleware/rate_limit_test.go
```

Scope is used for two things during verification:

* **Code-scan and invariant matching** — only invariants whose path scope intersects with these files run.
* **Drift detection** — if the PR ends up modifying additional files after submission, the verifier flags it as scope drift.

The agent populates scope from the actual diff, not from a forecast.

### Acceptance Criteria

A checklist of verifiable assertions. Each criterion is checked independently during verification.

```markdown
## Acceptance Criteria
- [ ] /api/v1/public/* endpoints enforce per-user rate limit
- [ ] Returns 429 when the per-user limit is exceeded
- [ ] Response includes Retry-After header on 429
- [ ] Rate-limit events log through the structured logger
- [ ] Internal endpoints (/api/v1/internal/*) are unaffected
```

Each criterion should:

* **State one claim.** "Returns 429 when exceeded" — not "Returns 429 when exceeded and logs the event." Split compound claims into separate criteria.
* **Be verifiable on its own.** A criterion the verifier can only check by reading the rest of the codebase will route to LLM fallback (less deterministic).
* **Avoid implementation language.** "Uses the new `RateLimiter` struct" is brittle. "Per-user limit is enforced before business logic runs" is durable.

See [Writing effective acceptance criteria](../how-to-guides/writing-effective-acceptance-criteria.md) for more guidance.

### Complete example

```markdown
# Add per-user rate limiting to public API

## Intent
Cap per-user request rate on /api/v1/public/* endpoints. On overflow,
return 429 with a Retry-After header so well-behaved clients can back
off. Existing internal endpoints are unaffected.

## Scope
- src/handlers/rate_limit.go
- src/middleware/chain.go
- tests/middleware/rate_limit_test.go

## Acceptance Criteria
- [ ] /api/v1/public/* endpoints enforce per-user rate limit
- [ ] Returns 429 when the per-user limit is exceeded
- [ ] Response includes Retry-After header on 429
- [ ] Rate-limit events log through the structured logger
- [ ] Internal endpoints (/api/v1/internal/*) are unaffected
```

### Reading a stored submission

Every submission is stored against the review URL and exported as part of the audit trail. The on-disk shape matches the markdown above plus metadata:

| Field            | Description                                                |
| ---------------- | ---------------------------------------------------------- |
| `title`          | The `#` heading                                            |
| `intent`         | The Intent section body                                    |
| `scope`          | The Scope file list, normalized to relative paths          |
| `criteria[]`     | The Acceptance Criteria list, one entry per checkbox        |
| `submitted_by`   | The user the MCP token belongs to                           |
| `submitted_at`   | ISO 8601 timestamp                                          |
| `repo`, `branch` | Git context the agent submitted from                        |
| `commit_sha`     | The HEAD commit at submission time                          |

The exported form is what auditors consume. The markdown is what reviewers read.

### See also

* [MCP tools](mcp-tools.md) — the submit-spec tool that produces this
* [Writing effective acceptance criteria](../how-to-guides/writing-effective-acceptance-criteria.md)
* [Concepts: Invariants](../concepts/invariants.md) — for assertions that should hold across changes
