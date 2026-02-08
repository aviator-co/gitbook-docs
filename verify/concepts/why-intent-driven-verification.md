# Why intent-driven verification

This document explains the reasoning behind Verify’s approach to code changes.

### The code review problem

Code review was designed when humans wrote code. A developer would spend hours or days writing a feature, then submit it for review. A reviewer could read the diff, understand the changes, and catch issues.

This worked because:

* Code was written slowly
* Diffs were human-sized
* Reviewers had time to think deeply

AI-generated code changes this equation.

### AI changes the math

AI tools can generate code in minutes. A developer describes what they want, and the AI produces an implementation. The code is often correct, but it’s also:

* Produced faster than it can be reviewed
* Harder to evaluate (is this what I actually asked for?)
* Easy to accumulate without careful inspection

Teams face a choice:

1. Review everything carefully → become a bottleneck
2. Review quickly → miss issues
3. Skip review for AI code → lose oversight entirely

None of these are good options.

### The wrong question

Traditional code review asks: **“Does this code look okay?”**

A reviewer reads the diff, applies their experience, and makes a judgment. But this question has problems:

* “Okay” is subjective
* Different reviewers have different standards
* The reviewer might not know what the code was _supposed_ to do
* Fatigue leads to rubber-stamping

For AI-generated code, the question is even harder. The code is often clean and idiomatic—the AI is good at that. But is it _correct_? Does it do what was intended?

### A better question

Verify asks a different question: **“Does this code do what we agreed it should do?”**

This question requires:

1. Agreeing on what the code should do (the spec)
2. Checking whether the implementation matches (verification)

The spec is the contract. Verification is the enforcement.

### Why specs work

Specs work because they shift the review earlier:

**Traditional flow:**

1. Developer writes code
2. Reviewer evaluates code
3. Arguments about whether it’s right

**Spec-driven flow:**

1. Developer writes spec
2. Reviewer approves spec
3. Developer (or AI) implements
4. Verification checks against spec

The hard conversation—“what should this do?”—happens before code is written. Once agreed, verification is mechanical.

### Reviewers approve intent, not implementation

Reviewing a spec is faster than reviewing code because:

* Specs are shorter
* Specs describe _what_, not _how_
* The reviewer isn’t evaluating implementation choices
* There’s a clear checklist of requirements

A reviewer can approve a spec in minutes. They’re answering: “If code satisfies these criteria, should it merge?”

They’re not answering: “Is this the best way to implement this? Are there bugs I’m missing? Did the developer forget something?”

Those questions are answered by verification against the agreed spec.

### Verification is systematic

Human review is sampling. A reviewer looks at code and uses judgment. They might catch issues, they might not. Different reviewers catch different things.

Verification is systematic. Every criterion in the spec is checked, every time. If the spec says “no hardcoded credentials,” verification checks for hardcoded credentials. Not “probably checks” or “might notice”—checks.

This doesn’t mean verification is perfect. If a criterion is missing from the spec, verification won’t check it. But for what’s in the spec, verification is thorough.

### Audit trails come free

Because specs are explicit and verification is recorded, you get audit trails automatically:

* What was the approved intent?
* Who approved it?
* Did the implementation match?
* What was checked?

This matters for compliance. Traditional code review produces a comment thread. Spec-driven verification produces a proof chain.

### When specs don’t make sense

Specs add overhead. For some changes, that overhead isn’t worth it:

* Typo fixes
* Documentation updates
* Trivial config changes

Verify lets you exempt paths from requiring specs. Not everything needs this level of rigor.

### The mental model

Think of specs like building permits:

* Before construction, you submit plans
* Plans are reviewed and approved
* Construction proceeds according to plans
* Inspection verifies the building matches plans

You don’t approve construction by watching workers hammer nails. You approve plans, then verify the result matches.

Code works the same way. Approve the intent, verify the implementation.

### See also

* [Tutorial: Your first spec](../your-first-spec.md)
* [How verification works ](how-verification-works.md)
* [Audit trails and compliance](audit-trails-and-compliance.md)
