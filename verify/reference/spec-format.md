# Spec format

This page documents the spec format used when the Aviator MCP submits a runbook via `specSubmit`. The agent typically generates this from your conversation; documented here so you can read submissions, hand-edit when needed, or build tools against the structure.

For the conceptual flow, see [How Verify works](../how-it-works.md). For the MCP tool itself, see [MCP tools](mcp-tools.md).

### Structure

A spec is markdown. Three sections are parsed; one is optional:

```markdown
# Title

## Intent
[plain-language description]

## Scope
[scope declarations using modify/call/forbid]

## Execution Steps
[optional — implementation plan]

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

### Scope

Declares what the change is allowed and not allowed to touch. Uses three keywords; the parser picks them up case-insensitively:

| Keyword  | Purpose                                  |
| -------- | ---------------------------------------- |
| `modify` | Files the change is allowed to edit.     |
| `call`   | External services or modules invoked.    |
| `forbid` | Files or patterns explicitly off-limits. |

```markdown
## Scope
modify: src/handlers/rate_limit.go, src/middleware/chain.go, tests/middleware/rate_limit_test.go
call: redis
forbid: src/auth/*, src/db/migrations/*
```

Comma-separated lists. Glob patterns are allowed (`src/foo/**/*.go`). Multiple lines with the same keyword accumulate.

### Execution Steps (optional)

If the spec includes an implementation plan, write it as a level-2 `## Execution Steps` section. The agent will use this to structure the runbook. Omit when the agent should plan from the intent alone.

```markdown
## Execution Steps

### Step 1: Add the rate-limit middleware
#### 1.1: Implement per-user counter
- Use a sliding-window counter keyed by user ID
- Backed by Redis

### Step 2: Wire it into the public routes
#### 2.1: Update the middleware chain
- Insert the rate-limit middleware before the handler chain
- Scope to /api/v1/public/*
```

Step headers are `### Step N: <Title>` (level-3) and `#### N.N: <Title>` (level-4). The same structure is reproduced in the runbook plan.

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

## Scope
modify: src/handlers/rate_limit.go, src/middleware/chain.go, tests/middleware/rate_limit_test.go
call: redis
forbid: src/auth/*, src/db/migrations/*

## Acceptance Criteria
- /api/v1/public/* endpoints enforce per-user rate limit
- Returns 429 when the per-user limit is exceeded
- Response includes Retry-After header on 429
- Rate-limit events log through the structured logger
- Internal endpoints (/api/v1/internal/*) are unaffected
```

### Multiple spec files

`specSubmit` accepts a list of spec files (`spec_files` parameter). For most changes one file is enough. Split into multiple files only when the change spans clearly separable concerns and each file is itself coherent. Preserve original filenames when the spec came from an existing file.

### See also

* [MCP tools](mcp-tools.md) — the `specSubmit` tool that submits this
* [Writing effective acceptance criteria](../how-to-guides/writing-effective-acceptance-criteria.md)
* [Concepts: Invariants](../concepts/invariants.md) — for assertions that should hold across changes
