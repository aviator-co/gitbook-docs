---
icon: shield-check
---

# Verify

Aviator Verify replaces line-by-line code review with verification of *what the change is supposed to do*. You work with your agent locally. Aviator captures your intent through a single MCP call, runs every acceptance criterion against the running code, and produces a review document with verdicts and evidence.

The reviewer judges behavior — not the diff.

You can start using Verify with code-scan alone — no preview, no infrastructure setup. A preview is what unlocks **runtime verification**: scenarios driven against your running code. Most teams get value from day one with code-scan, then add a preview when behavioral criteria start mattering.

### The flow

1. **Build with your agent.** Implement the task with Cursor, Claude, Copilot — anything you use today. No new tooling, no behavior change.
2. **Submit intent via MCP.** The agent calls one tool when you're done. It captures the intent and the acceptance criteria from what was built.
3. **Aviator verifies end to end.** Scenarios run in your preview, invariants apply automatically, code-scan handles structural checks.
4. **Review the behavior.** The reviewer sees the intent, the verdict per criterion, and the evidence. They approve, waive with reason, or ask for another scenario on the spot.

→ [How it works](how-it-works.md) — the full narrative, with diagrams.

### Invariants — the standing rules

Every code review has a long tail of repeated comments. *Use the structured logger. No direct user-table writes. Errors emit a metrics counter. Cap external dependencies. Stripe amounts use the Money type.* Reviewers say the same thing on PR after PR, and any one of them missed in any one review becomes a future bug or migration.

Verify turns those into **invariants** — team-defined rules in an account-level catalog that apply to every matching change automatically. When a runbook is created, an LLM selector picks which invariants legitimately apply to this change, and they're materialized as acceptance criteria alongside the user-supplied ones. From there they flow through the same verifier pipeline — code-scan or runtime — and produce verdicts on the same review surface.

How invariants get into your catalog:

* **Mine your PR history.** Aviator's AI reads your team's actual PR review comments and proposes invariants from the patterns it sees. The highest-leverage onboarding path — your team is already enforcing these rules in review, just not encoding anywhere.
* **Extract from your docs.** `CONTRIBUTING.md`, `LLM.md`, and similar files become drafts.
* **Adopt from templates.** Aviator's starter library covers the common categories: security baseline, observability, data access, backwards compatibility.
* **Author manually.** Admins write the rule directly.

All AI-drafted invariants land in draft status. An admin reviews each before it goes active. Failed invariant verdicts can be waived by the reviewer with a category (`false_positive`, `doesnt_apply`, `accepted_risk`, `fix_in_followup`) — every waiver is recorded in the audit trail.

→ [Concepts: Invariants](concepts/invariants.md)

### Why this approach

Code review was designed when humans wrote code slowly. AI changes that. Code generates in minutes; reviewing it line by line still takes the same hour as before. The shift:

* **Traditional review asks:** *Does this code look okay?*
* **Verify asks:** *Does this code do what we agreed it should do?*

Three things follow:

* **Faster reviews.** A reviewer judges intent and evidence, not 800 lines of diff.
* **Systematic checking.** Every criterion is verified, every time — no sampling, no fatigue.
* **Complete audit trail.** Every change links intent → verifier verdict → evidence → reviewer decision.

→ [Why intent-driven verification](concepts/why-intent-driven-verification.md)

### Core concepts

| Concept                                                                | What it is                                                                              |
| ---------------------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| [Intent and acceptance criteria](reference/spec-format.md)             | What the agent submits — what the change is for, and the verifiable assertions it must satisfy. |
| [Invariants](concepts/invariants.md)                                   | Team-defined rules applied to every matching change. Security baseline, data access, conventions. |
| [Previews](concepts/previews.md)                                       | Ephemeral environments scenarios run against. Per-run, torn down after.                 |
| [Verification layers](concepts/verification-layers.md)                 | How criteria, invariants, and domain contracts stack.                                   |
| [Audit and compliance](concepts/audit-trails-and-compliance.md)        | Immutable trail of every step — submission, verdicts, evidence, decisions.              |

### Getting started

1. [Connect a repository](how-to-guides/connect-a-repository.md) — wire up GitHub.
2. [Your first verification](your-first-spec.md) — a 15-minute hands-on. Install the MCP, ship a small change, watch verification run. No preview needed.
3. [Setting up org invariants](setting-up-org-invariants.md) — codify the rules every change must respect.
4. [Creating a preview](how-to-guides/creating-a-preview.md) — *optional.* Unlocks runtime verification when your criteria start asking about behavior the diff alone can't answer.

### Quick links

* **Writing for the agent:** [SKILL.md guide](how-to-guides/writing-a-skill-md.md) · [Effective acceptance criteria](how-to-guides/writing-effective-acceptance-criteria.md)
* **Configuration:** [Preview YAML](reference/preview-yaml.md) · [Spec format](reference/spec-format.md)
* **Operations:** [Managing previews](how-to-guides/managing-previews.md) · [Seed data for previews](how-to-guides/seed-data-for-previews.md)
* **When things go wrong:** [Fixing verification failures](how-to-guides/fixing-verification-failures.md)
