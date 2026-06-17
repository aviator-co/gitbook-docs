# Spec format

This page documents the shape of what `specSubmit` captures: a **title**, the **intent**, and the **acceptance criteria**. The agent typically generates this from your conversation; documented here so you can read submissions, hand-edit when needed, or build tools against the structure.

For the conceptual flow, see [How Verify works](../how-it-works.md). For the MCP tool itself, see [MCP tools](mcp-tools.md).

### Structure

A spec is markdown. Two sections are parsed:

```markdown
# Title

## Intent
[plain-language description]

## Acceptance Criteria
[bullet list of verifiable assertions]
```

Section headings must be level-2 (`##`). The parser is case-insensitive on the heading text.

### Title

A short description of the change. Appears in the dashboard, runbook UI, and audit trail.

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

### Acceptance Criteria

A bullet list of verifiable assertions. Each criterion is checked independently during verification.

```markdown
## Acceptance Criteria
- /api/v1/public/* endpoints enforce per-user rate limit
- Returns 429 when the per-user limit is exceeded
- Response includes Retry-After header on 429
- Rate-limit events log through the structured logger
- Internal endpoints (/api/v1/internal/*) are unaffected
```

Bullet markers are `-` or `*`. Checkbox syntax (`- [ ]`) is accepted but not required — the checkbox is dropped during parsing.

Each criterion should:

* **State one claim.** "Returns 429 when exceeded" — not "Returns 429 when exceeded and logs the event." Split compound claims.
* **Be verifiable on its own.** A criterion that requires reading the rest of the codebase to evaluate will route to a less-deterministic verifier path.
* **Avoid implementation language.** "Uses the new `RateLimiter` struct" is brittle. "Per-user limit is enforced before business logic runs" is durable.

See [Writing effective acceptance criteria](../how-to-guides/writing-effective-acceptance-criteria.md) for more guidance.

### Complete example

```markdown
# Add per-user rate limiting to public API

## Intent
Cap per-user request rate on /api/v1/public/* endpoints. On overflow,
return 429 with a Retry-After header so well-behaved clients can back
off. Existing internal endpoints are unaffected.

## Acceptance Criteria
- /api/v1/public/* endpoints enforce per-user rate limit
- Returns 429 when the per-user limit is exceeded
- Response includes Retry-After header on 429
- Rate-limit events log through the structured logger
- Internal endpoints (/api/v1/internal/*) are unaffected
```

### See also

* [MCP tools](mcp-tools.md) — the `specSubmit` tool that submits this
* [Writing effective acceptance criteria](../how-to-guides/writing-effective-acceptance-criteria.md)
* [Concepts: Invariants](../concepts/invariants.md) — for assertions that should hold across changes
