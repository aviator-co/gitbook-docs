# Why intent-driven verification

This document explains the reasoning behind Verify's approach to code changes.

### Code review was designed for human-written code

A developer would spend hours or days writing a feature, then submit it for review. A reviewer could read the diff, understand the changes, and catch issues.

This worked because:

* Code was written slowly
* Diffs were human-sized
* Reviewers had time to think deeply

AI-generated code breaks all three.

### AI changes the math

AI tools can generate code in minutes. A developer describes what they want; the AI produces an implementation. The code is often correct, but it's also:

* Produced faster than it can be reviewed line by line
* Harder to evaluate — *is this what I actually asked for?*
* Easy to accumulate without careful inspection

Teams face a bad set of choices:

1. Review everything carefully → reviewers become a bottleneck.
2. Review quickly → catch fewer issues.
3. Skip review for AI code → lose oversight.

None of these are good. They're all symptoms of the same problem: the *thing being reviewed* is wrong.

### The wrong question

Traditional review asks: **"Does this code look okay?"**

A reviewer reads the diff and makes a judgment. Problems with this:

* "Okay" is subjective.
* Reviewers have different standards.
* The reviewer often doesn't know what the code was *supposed* to do.
* Fatigue produces rubber-stamping.

For AI-generated code, the question gets harder, not easier. The code is often clean and idiomatic — AI is good at that. But is it *correct*? Does it match what was intended?

### The right question

Verify asks: **"Does this code do what we agreed it should do?"**

Answering that requires two things:

1. **A clear statement of what the change is for** — captured as the intent and the acceptance criteria the change must satisfy.
2. **Systematic checking that the running code matches** — verdicts and evidence for every criterion.

The intent is the contract. Verification is the enforcement. The reviewer judges *both* — was this the right intent, and did verification actually demonstrate it.

### Where reviewers actually spend their time

In the Verify flow, the agent implements the change, then submits the intent and acceptance criteria through the MCP. Verification runs. The reviewer opens a review document that contains:

* The intent.
* Every criterion with a verdict.
* The evidence behind each verdict — scenario output, matched invariants, code-scan diffs.

That's what the reviewer reads. They don't trawl the diff. They check that the intent reflects what was actually needed, and that the evidence convinces them the criteria were really met.

This is faster than diff review because:

* The intent is short.
* The evidence is concrete, not interpreted.
* The reviewer isn't second-guessing implementation choices the agent already made and the verifier already approved.
* There's a clear checklist — the criteria themselves.

A reviewer answers: *"If this evidence is convincing, should this merge?"* They don't answer: *"Is this the best way to implement this? Did the developer forget a case?"* The first verifiers caught what they could; the reviewer focuses on the residue — intent fit and judgment calls the verifier can't make.

### Verification is systematic

Human review is sampling. A reviewer reads the diff and uses judgment. They might catch issues, they might not. Different reviewers catch different things.

Verification is systematic. Every criterion runs. Every invariant that matches the change runs. If an invariant says "no hardcoded credentials," it checks — not "probably checks," *checks*. Same code + same intent → same verdicts.

This doesn't mean verification is perfect. If the agent missed a criterion in the intent, the verifier won't check it. But for what's submitted, verification is thorough — and the missed criterion is itself a reviewable surface: did the agent capture the right set of assertions?

### Audit trails come free

Because intent is explicit and verification is recorded, you get audit trails automatically:

* What was the submitted intent?
* Which criteria did the agent generate?
* Which verifier handled each, and what evidence did it produce?
* Who reviewed it, and what did they approve, waive, or send back?

Traditional code review produces a comment thread. Intent-driven verification produces a structured trail — the same record reviewers see is what auditors export.

→ [Audit trails and compliance](audit-trails-and-compliance.md)

### When verification doesn't make sense

Verification adds overhead. For some changes, that overhead isn't worth it:

* Typo fixes
* Documentation updates
* Trivial config changes

Verify lets you exempt paths from requiring verification. Not everything needs this level of rigor — and forcing it everywhere produces noise that erodes trust in the verdicts.

### The mental model

Think of verification like a building inspection.

A building isn't approved by an architect watching every nail. It's approved by an inspector evaluating the finished structure against the blueprints — with measurements, load tests, and photographic evidence to back the verdict. The architect signs off based on the inspector's report.

Intent-driven verification follows the same shape:

* **Blueprint:** the submitted intent and acceptance criteria.
* **Construction:** the agent's implementation.
* **Inspection:** the verifier pipeline, producing verdicts + evidence per criterion.
* **Sign-off:** the reviewer approves based on intent fit and evidence quality.

Reviewers don't watch every line of code get written. They evaluate what was built against what was agreed, with proof in the room.

### See also

* [How verification works](how-verification-works.md) — the inspection pipeline
* [How Verify works](../how-it-works.md) — the end-to-end loop
* [Audit trails and compliance](audit-trails-and-compliance.md)
