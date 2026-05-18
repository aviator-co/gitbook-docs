---
icon: shield-check
---

# Verify

Aviator Verify checks code changes against a structured spec instead of relying on line-by-line code review. Your team writes a spec that defines what the change should do; Verify checks the implementation against it on every push and posts the result back to GitHub.

Verify is in active development. This documentation describes what is currently shipping. Features that are planned but not yet built are not documented here.

### How it works

1. **Write a spec.** Describe the change in a structured format with scope and acceptance criteria. The AI assistant helps draft the spec from a natural-language description.
2. **Review the criteria.** A reviewer reads the spec and the acceptance criteria — the contract the implementation will be checked against. Comments refine the criteria.
3. **Implement.** Write the code using any tool: Cursor, Copilot, Claude, or manually. The spec is the contract.
4. **Verify.** Push your code. Verify checks the implementation against each acceptance criterion and any baseline invariants that apply to your repository. Results post to GitHub as a check.

### Why this approach

Code review was designed when humans wrote code slowly. AI changes that equation. Code can be generated in minutes, but reviewing it still takes just as long.

Traditional review asks: "Does this code look okay?"

Verify asks: **"Does this code do what we agreed it should do?"**

This shift has several advantages:

* **Faster reviews.** Reading a spec is faster than reading a diff. You're agreeing on _what_, not auditing _how_.
* **Coverage that compounds.** Baseline invariants — repo-wide rules — apply automatically on every PR. A rule added once runs forever.
* **A record of what was checked.** Each verification run records which criteria applied to the change and what the verdict was.

→ [Why intent-driven verification](concepts/why-intent-driven-verification.md)

### Core concepts

#### Specs

A spec defines what a change should accomplish. The parser currently recognizes two structured sections:

| Section                 | Purpose                                    |
| ----------------------- | ------------------------------------------ |
| **Scope**               | Files and services the change may touch    |
| **Acceptance Criteria** | Specific, verifiable requirements          |

A spec also typically includes an Intent section (free-form description) and Execution Steps (implementation breakdown). These are useful for human reviewers and for AI implementation tools but are not parsed by Verify.

```markdown
# Add subscription status endpoint

## Intent
Authenticated endpoint for retrieving user subscription status
without exposing internal billing identifiers.

## Scope
- **modify:** `src/handlers/subscription.go`, `src/models/subscription.go`
- **call:** `subscription-service`
- **forbid:** `src/auth/*`, database migrations

## Acceptance Criteria
- [ ] Endpoint: `GET /api/v1/subscription/status`
- [ ] Requires authentication
- [ ] Response includes: status, renewal_date, plan_name
- [ ] Response excludes: internal_id, billing_provider_id
- [ ] Returns 404 if subscription not found
```

Specs are created with AI assistance and edited collaboratively in chat. Acceptance criteria can be updated at any time; subsequent verification runs use the current set.

→ [Tutorial: Your first spec](your-first-spec.md)

→ [Reference: Spec format](reference/spec-format.md)

#### Verification

When you push code, Verify checks it against the spec's acceptance criteria together with any baseline invariants that apply.

Verification today is LLM-based: the engine runs an AI agent (Claude) in read-only mode against the diff and the criteria, and the agent reports per-criterion verdicts. Verdicts are not deterministic — two runs may differ in wording or in edge-case judgements. This is a known limitation; deterministic and behavioral verification are on the roadmap.

Results appear as a GitHub PR check named `aviator/verify` with a markdown summary listing each criterion's verdict.

→ [How verification works](concepts/how-verification-works.md)

→ [Understanding verification results](reference/understanding-verification-results.md)

#### Baseline invariants

Some requirements apply to many changes in a repository — "all HTTP handlers require auth," "no hardcoded secrets," "all GraphQL mutations log to the audit table." Rather than restating these in every spec, they live as **baseline invariants** on the repo.

Each invariant is a standing rule with conditions that determine which PRs it applies to (file paths, languages, change types). When a PR matches, the invariant becomes an acceptance criterion for that PR's verification.

Baseline invariants are seeded from your repo's existing signals (CLAUDE.md, contributing docs, conventions discovered by analysis) during onboarding, and added to over time.

→ [Tutorial: Setting up baseline invariants](setting-up-org-invariants.md)

#### A record of what was checked

Each verification run records:

* The spec that applied
* The acceptance criteria checked
* The verdict for each criterion, with evidence and location
* The commit SHA and PR

This record is available in the dashboard and surfaced in the GitHub check. It is not currently exportable for compliance reporting. Audit-grade export is on the roadmap.

### Getting started

The fastest way to understand Verify is to use it on a real repo:

1. [Tutorial: Your first spec](your-first-spec.md) — Create a spec, see verification in action
2. [How-to: Connect a repository](how-to-guides/connect-a-repository.md) — Set up GitHub integration
3. [Tutorial: Setting up baseline invariants](setting-up-org-invariants.md) — Add repo-wide rules

### Quick links

* New to Verify? Start with [Your first spec](your-first-spec.md)
* Setting up? See [Connect a repository](how-to-guides/connect-a-repository.md)
* Verification failed? Check [Fix verification failures](how-to-guides/fixing-verification-failures.md)
