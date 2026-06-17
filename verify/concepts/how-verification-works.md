# How verification works

This document goes deeper than the [How it works](../how-it-works.md) narrative. It covers what actually happens inside a verification run — how criteria are classified, how each verifier produces evidence, and why the same code always yields the same verdict.

### A verification run, end to end

A run starts when the agent submits an intent through the MCP. Aviator does three things in order:

1. **Constructs the run.** Pulls the change set (the diff against the target branch), the submitted criteria, and the invariants that match the change. Allocates a preview if any criterion will need one.
2. **Routes each check through the pipeline.** Every criterion and matching invariant runs through the verifier pipeline. They're independent — verdicts are produced in parallel where possible.
3. **Compiles the results.** Verdicts, evidence, and links to the preview are assembled into the review document.

The whole run is observable in real time — the review document streams updates as verdicts land.

### The criterion pipeline

<figure><img src="../../.gitbook/assets/verify-pipeline.svg" alt="Each criterion is classified and routed to a single verifier"><figcaption><p>One criterion → one verifier → one verdict with evidence</p></figcaption></figure>

Each criterion goes through exactly one verifier. A classifier picks the verifier based on the criterion text and the files the change touched.

| Verifier         | Picked when…                                                                          | Produces                                  |
| ---------------- | ------------------------------------------------------------------------------------- | ----------------------------------------- |
| **Code-scan**    | The criterion is a structural assertion — file scope, dependency surface, function signature, type. | The diff or AST snippet that proves the claim. |
| **Scenario**     | The criterion is behavioral — endpoint contract, error shape, side effect, latency.   | The actual request/response (or trace) from a preview run. |
| **Invariant**    | The check matches a team-defined rule — security, data access, conventions.           | The matched rule and the offending snippet (if violated). |
| **LLM fallback** | The criterion is too custom for the other verifiers — judgment calls, multi-step reasoning. | A reasoning trace with cited code spans. |

The classifier favors Code-scan and Scenario over LLM fallback. Determinism is highest for code-scan, lowest for LLM — so the pipeline minimizes the cases that route to the fallback.

If you read a verdict and disagree with the verifier that handled it, that's signal — usually the criterion was written ambiguously enough to land in the wrong path. Tightening the criterion text usually moves it back.

### How a scenario actually runs

Scenarios are the most expensive verifier path. They need:

1. A **preview** to run against (see [Concepts: Previews](previews.md)).
2. A **skill set** that tells the agent how to operate the preview — base URL, test users, fixtures (see [Writing a SKILL.md](../how-to-guides/writing-a-skill-md.md)).
3. The **criterion text** — the specific claim being verified.

The runner constructs a small program tailored to the criterion: set up the preconditions, exercise the endpoint or function, capture the response, compare to the expected shape. It records what it did and what came back as the evidence.

This is the part most teams underinvest in early — a thin SKILL.md and stale seed data produce scenario verdicts you can't trust. The pipeline runs fast, but the verdict is only as good as the preview it ran against.

### How invariants compose with criteria

Invariants run alongside criteria. They're not part of the criterion list — they're applied automatically based on the change set.

The composition rule: a run passes only if every criterion passes *and* every matching invariant passes. Failures stack — one failed criterion plus two failed invariants produces three verdicts on the same review document, not one merged failure.

See [Verification layers](verification-layers.md) for how criteria and invariants combine in practice, and [Invariants](invariants.md) for the deeper concept.

### Evidence, not output

Every verdict carries evidence. The shape depends on the verifier:

| Verifier      | Evidence shape                                                                |
| ------------- | ----------------------------------------------------------------------------- |
| Code-scan     | File + line range, plus the snippet. Optionally a parsed view (AST node).     |
| Scenario      | Request, response, and timing. For multi-step scenarios, the full transcript. |
| Invariant     | The rule that matched, the file + line that violates it, suggested direction. |
| LLM fallback  | Reasoning trace with code citations. Lower confidence — flag for human review.|

Evidence is the reviewer's primary surface. A verdict without evidence isn't useful — "trust me, it passed" defeats the point. If a verdict ever appears without evidence, file it as a bug.

### Determinism

Verification is deterministic for code-scan, scenario, and invariant verdicts. Run the same change against the same intent twice — same verdicts, same evidence.

LLM fallback verdicts are not strictly deterministic. They're cached on the criterion + change set so re-runs of the same content produce stable results, but a meaningfully different rephrasing of a criterion may route differently or arrive at a different verdict. Treat fallback verdicts as advisory and review them by hand.

Why determinism matters:

* You can reproduce failures.
* Re-runs after no changes don't flip results.
* Audit records are reliable across time.

### Performance

A typical run completes in 30–120 seconds:

| Factor                  | Effect                                                                  |
| ----------------------- | ----------------------------------------------------------------------- |
| Diff size               | Larger diffs → more code-scan + invariant checks. Sub-linear.           |
| Number of criteria      | One verifier per criterion. Linear; parallelized where possible.        |
| Scenarios per run       | Each scenario boots in the preview. Dominates total time once present.  |
| Preview cold-start      | Most of the variance. Bake heavy work into the image — see [Managing previews](../how-to-guides/managing-previews.md). |
| Matching invariant count| Linear; usually cheap. Most invariants resolve from the diff.           |

If a run is slow, look at the scenario count and the preview boot time. Those two dominate.

### Comparison with other approaches

**vs. static analysis.** Static analysis tools (linters, type checkers) verify syntax and patterns. Verify checks semantic intent against running behavior. Static analysis says "this variable is unused." Verify says "this endpoint doesn't return what the intent said it should." Use both — they catch different things.

**vs. tests.** Tests assert that specific inputs produce specific outputs, written by the developer. Verify runs scenarios generated from the submitted intent against an ephemeral preview. Tests check what you remembered to write a test for. Verify checks what you said the change was supposed to do. Tests catch regressions; verification catches intent drift.

**vs. AI code review.** AI code review tools leave comments on diffs. They're faster than human review but inherit the same limitations: no record of intent, no systematic coverage, sampling-based, no audit trail. Verify starts from declared intent and produces structured verdicts with evidence.

### See also

* [How Verify works](../how-it-works.md) — the end-to-end narrative
* [Invariants](invariants.md)
* [Previews](previews.md)
* [Verification layers](verification-layers.md)
