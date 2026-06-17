---
icon: shield-check
---

# Verify

Aviator Verify replaces line-by-line code review with verification of *what the change is supposed to do*. You work with your agent locally. Aviator captures your intent through a single MCP call, runs every acceptance criterion against the running code, and produces a review document with verdicts and evidence.

The reviewer judges behavior — not the diff.

### The flow

1. **Build with your agent.** Implement the task with Cursor, Claude, Copilot — anything you use today. No new tooling, no behavior change.
2. **Submit intent via MCP.** The agent calls one tool when you're done. It captures the intent and the acceptance criteria from what was built.
3. **Aviator verifies end to end.** Scenarios run in your preview, invariants apply automatically, code-scan handles structural checks.
4. **Review the behavior.** The reviewer sees the intent, the verdict per criterion, and the evidence. They approve, waive with reason, or ask for another scenario on the spot.

→ [How it works](how-it-works.md) — the full narrative, with diagrams.

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

1. [Your first verification](your-first-spec.md) — a 15-minute hands-on. Install the MCP, ship a small change, watch verification run.
2. [Connect a repository](how-to-guides/connect-a-repository.md) — wire up GitHub.
3. [Creating a preview](how-to-guides/creating-a-preview.md) — give scenarios something to run against.
4. [Setting up org invariants](setting-up-org-invariants.md) — codify the rules every change must respect.

### Quick links

* **Writing for the agent:** [SKILL.md guide](how-to-guides/writing-a-skill-md.md) · [Effective acceptance criteria](how-to-guides/writing-effective-acceptance-criteria.md)
* **Configuration:** [Preview YAML](reference/preview-yaml.md) · [Spec format](reference/spec-format.md)
* **Operations:** [Managing previews](how-to-guides/managing-previews.md) · [Seed data for previews](how-to-guides/seed-data-for-previews.md)
* **When things go wrong:** [Fixing verification failures](how-to-guides/fixing-verification-failures.md)
