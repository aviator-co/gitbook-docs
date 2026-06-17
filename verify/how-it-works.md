# How Verify works

Verify replaces line-by-line code review with verification of *what the change is supposed to do*. You work with your agent locally. Aviator captures your intent through an MCP call, runs every acceptance criterion against the running code, and produces a review document with verdicts and evidence.

<figure><img src="../.gitbook/assets/verify-how-it-works.svg" alt="Four-stage Verify workflow: build, submit intent, verify, review"><figcaption><p>The end-to-end Verify loop</p></figcaption></figure>

The full loop:

1. **Build with your agent.** Implement the task with Cursor, Claude, Copilot, or anything else you use today. No new tooling, no behavior change.
2. **Submit intent via the Aviator MCP.** When you're done, the agent calls one MCP tool. It captures the intent you and the agent agreed on and the acceptance criteria from what was built.
3. **Aviator verifies end to end.** Every criterion runs through the verification pipeline. Code-scan handles structural checks against the diff — this works out of the box. Runtime checks run against a preview environment if you've configured one (optional; see [Concepts: Previews](concepts/previews.md)). Matching invariants apply automatically.
4. **Review the behavior.** The reviewer sees the intent, the verdict per criterion, and the evidence behind each verdict. They can approve, waive with reason, or ask for another scenario on the spot.

### What gets submitted

The MCP call carries two things:

- **Intent** — a short statement of *what* this change is for. It carries the constraints that aren't visible from the diff alone.
- **Acceptance criteria** — verifiable assertions the change must satisfy. Each criterion is independent and has a clear pass/fail shape.

The agent generates both from what was actually built. You submit *after* implementing, not before — there is no separate "approve the spec first" step.

### The verification pipeline

Every criterion runs through exactly one verifier. A classifier picks the verifier based on the criterion text and the files the change touched.

<figure><img src="../.gitbook/assets/verify-pipeline.svg" alt="Verification pipeline: criterion is classified and routed to one of two verifier paths"><figcaption><p>Each criterion is classified and routed to a single verifier path</p></figcaption></figure>

| Verifier      | Used for                                                                          | Evidence captured                                                       |
| ------------- | --------------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| **Code-scan** | Structural assertions — file scope, dependency surface, function signatures       | Diff or AST snippets that demonstrate the assertion                     |
| **Runtime**   | Behavioral assertions — endpoint contracts, error shapes, side effects, UI behavior | Screenshots, console logs, DOM snapshots, API responses, full traces |

Invariants — your team-defined rules — flow through the same pipeline. When an invariant matches a change, it's materialized as an acceptance criterion on the runbook and verified by whichever path fits.

→ [Concepts: Invariants](concepts/invariants.md)

### Why previews matter (and why they're optional)

Previews are optional. Verify works on day one with code-scan alone — the classifier routes every criterion to code-scan when no preview is configured, and you get verdicts on structural assertions from the start.

A preview unlocks **runtime verification**: behavioral criteria — endpoint contracts, UI flows, error shapes — need the code to actually run. Aviator builds an ephemeral preview environment per run: it loads your image, injects secrets, runs a setup script, and exposes the port the scenarios hit. The reviewer can also open the preview from the review document to explore it manually.

Most teams start without a preview and add one when behavioral criteria start mattering. A repo can declare multiple previews — staging, sandbox, prod-mirror — and tag scenarios to the one they need.

### The review document

The review document is the surface reviewers actually use. Three regions:

1. **Intent** — what you and the agent agreed to build.
2. **Evidence** — every criterion with its verdict and the proof.
3. **Decisions** — request another scenario, open the preview, approve, or waive with reason.

The reviewer is judging behavior and intent — not the diff. The diff is still there if you want it, but it's not the primary surface.

### Audit and compliance

Every step is recorded as an immutable event: intent submitted, who submitted it, criteria generated, verifier and verdict per criterion, evidence captured, reviewer decisions, exceptions and waivers. Compliance teams export this log as a single audit trail per change.

→ [Audit trails and compliance](concepts/audit-trails-and-compliance.md)

### Running with remote agents

If you'd rather not run the agent locally, Aviator Runbooks runs the implementing agent inside a sandbox, calls the same MCP, and waits for verification. The verification path is identical — only the agent's location changes. Useful for batch work, off-hours runs, or non-developer-driven changes.

### See also

- [Setting up org invariants](setting-up-org-invariants.md)
- [Concepts: Invariants](concepts/invariants.md)
- [How to: Writing a SKILL.md](how-to-guides/writing-a-skill-md.md)
- [Concepts: Verification layers](concepts/verification-layers.md)
- [Reference: Spec format](reference/spec-format.md)
