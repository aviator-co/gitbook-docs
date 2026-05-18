# Why intent-driven verification

This document explains the reasoning behind Verify's approach to code changes.

### The code review problem

Code review was designed when humans wrote code. A developer would spend hours or days writing a feature, then submit it for review. A reviewer could read the diff, understand the changes, and catch issues.

This worked because:

* Code was written slowly
* Diffs were human-sized
* Reviewers had time to think deeply

AI-generated code changes this equation.

### AI changes the math

AI tools can generate code in minutes. A developer describes what they want, and the AI produces an implementation. The code is often correct, but it's also:

* Produced faster than it can be reviewed
* Harder to evaluate (is this what I actually asked for?)
* Easy to accumulate without careful inspection

Teams face a choice:

1. Review everything carefully → become a bottleneck
2. Review quickly → miss issues
3. Skip review for AI code → lose oversight entirely

None of these are good options.

### The wrong question

Traditional code review asks: **"Does this code look okay?"**

A reviewer reads the diff, applies their experience, and makes a judgment. But this question has problems:

* "Okay" is subjective
* Different reviewers have different standards
* The reviewer might not know what the code was _supposed_ to do
* Fatigue leads to rubber-stamping

For AI-generated code, the question is even harder. The code is often clean and idiomatic—the AI is good at that. But is it _correct_? Does it do what was intended?

### A better question

Verify asks a different question: **"Does this code do what we agreed it should do?"**

This question requires:

1. Agreeing on what the code should do (the spec)
2. Checking whether the implementation matches (verification)

The spec is the contract. Verification is the enforcement.

### Why specs work

Specs work because they shift the review earlier:

**Traditional flow:**

1. Developer writes code
2. Reviewer evaluates code
3. Arguments about whether it's right

**Spec-driven flow:**

1. Developer writes spec
2. Reviewer reads the acceptance criteria, suggests changes
3. Developer (or AI) implements
4. Verification checks against the criteria

The hard conversation — "what should this do?" — happens before or alongside the implementation. Once the criteria are settled, the check is mechanical.

### Reviewing the criteria, not the code

Reading acceptance criteria is faster than reading code because:

* Criteria are shorter than implementations
* Criteria describe _what_, not _how_
* The reviewer isn't evaluating implementation choices
* There's a clear checklist of requirements

A reviewer can read a set of criteria in minutes. They're answering: "If code satisfies these criteria, should it merge?"

They're not answering: "Is this the best way to implement this? Are there bugs I'm missing? Did the developer forget something?"

Those questions are answered by verification against the agreed criteria.

### Coverage that compounds

A human reviewer reads a diff and uses judgment. They might catch issues, they might not. The same kind of mistake gets caught — or missed — independently on every future PR.

Baseline invariants change that. A rule added to the repository once applies to every matching PR going forward. The set grows over time as your team discovers new things to check for. Coverage compounds.

This doesn't mean verification is perfect. If a rule is missing, verification won't check it. But every catch that becomes an invariant runs forever.

### A record of what was checked

Because the criteria are explicit and verification results are recorded, each change has a clear record:

* What were the acceptance criteria?
* What did each criterion's verdict say?
* What evidence did the verifier cite?

This is more useful than a comment thread on a PR. It's not yet a compliance-grade audit export — that's on the roadmap — but it gives reviewers and authors a clearer trail than traditional review produces.

### When specs don't make sense

Specs add overhead. For some changes, that overhead isn't worth it:

* Typo fixes
* Documentation updates
* Trivial config changes

For these, traditional review remains appropriate. Verify works best when there's a non-trivial change with multiple acceptance criteria worth checking.

### The mental model

Think of specs like building permits:

* Before construction, you submit plans
* Plans are reviewed and approved
* Construction proceeds according to plans
* Inspection verifies the building matches plans

You don't approve construction by watching workers hammer nails. You approve plans, then verify the result matches.

Code works the same way. Approve the intent, verify the implementation.

### See also

* [Tutorial: Your first spec](../your-first-spec.md)
* [How verification works](how-verification-works.md)
