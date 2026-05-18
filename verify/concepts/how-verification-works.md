# How verification works

This document explains what happens when Verify checks code against a spec.

### What runs

When a PR linked to a spec is pushed, Verify:

1. Loads the spec's current acceptance criteria
2. Composes the set of baseline invariants that apply to the changed files
3. Runs an AI agent (Claude) in read-only mode against the diff, the criteria, and the scope declaration
4. Records a per-criterion verdict with evidence and location
5. Posts a summary as a GitHub check named `aviator/verify`

Each verification produces a `VerificationRun` record with a `VerificationResult` per criterion.

### LLM-based verification

Verification today is driven by an AI agent. The engine sends the agent the criteria, the diff, and contextual files; the agent returns structured JSON with one verdict per criterion.

This has implications worth being explicit about:

* **Verdicts are not deterministic.** Running the same PR twice may produce slightly different wording, and edge-case judgements can differ. The engine reuses results from prior runs at the same commit SHA when the criteria are unchanged, to reduce drift.
* **The agent reasons over the code.** It is not pattern-matching or running tests. It reads the diff and surrounding code to decide whether the implementation appears to satisfy each criterion.
* **Evidence is text.** Each result includes the verifier's reason, a location (file and line where available), and a confidence flag.

Deterministic verification — where verdicts are pure functions over captured artifacts — and behavioral verification — where an agent exercises the running system and pure code judges the result — are on the roadmap. They are not in the current implementation.

### Acceptance criteria

Each criterion in the spec is evaluated independently. The agent attempts to confirm whether the implementation satisfies the requirement using the diff and the surrounding code.

For example, if a criterion says "Response excludes internal_id":

1. Find what the endpoint returns
2. Trace data flow through transformations
3. Decide whether `internal_id` could appear in the response

This is more thorough than a regex over the diff. It is less reliable than a deterministic check.

#### Criterion results

Each criterion gets one of three results:

* **Pass** — Implementation appears to satisfy the requirement
* **Fail** — Implementation appears to violate the requirement, with a reason
* **Error** — The verifier could not produce a verdict (e.g., context too large, tool error)

Failed criteria include:

* The reason for failure
* File and line number (when the verifier could identify them)
* A short evidence snippet

### Baseline invariants

In addition to spec-specific criteria, Verify composes any **baseline invariants** that apply to the changed files. Invariants are repo-wide rules with conditions (file paths, languages, change types) that determine when they apply.

Applicable invariants are merged into the acceptance criteria set for the run. They are not checked as a separate stage — from the verifier's perspective, they're additional criteria.

This means a single run produces a unified list of verdicts: spec criteria + applicable invariants.

### Scope

The spec's `Scope` section is parsed and made available to the verifier as context. It lists the files and services the change may touch (`modify`, `call`, `forbid`).

Scope is **not** currently enforced as a separate gate before semantic verification. If you modify a file outside the declared scope, the verifier can flag it as part of its judgement, but there is no hard pre-check that blocks verification on undeclared modifications.

### Results

After verification completes, the engine:

* Persists the `VerificationRun` and per-criterion `VerificationResult` rows
* Updates the GitHub check (`aviator/verify`) with a markdown summary
* Publishes a websocket update for the dashboard

The GitHub check links back to the runbook detail page, where you can see each criterion's verdict, reason, and evidence.

### What happens on a re-push

Each new commit on the PR re-triggers verification. The engine attempts to reuse results from the prior run at the same criteria set when the criterion text is unchanged, which reduces churn on re-pushes that don't change the criteria.

### Performance

Verification typically completes in 30–90 seconds for moderate-sized PRs. Larger diffs, more criteria, and more complex codebases all increase latency.

### Comparison with other approaches

#### vs. Static analysis

Static analysis tools (linters, type checkers) verify syntax and patterns deterministically. Verify uses an AI agent to reason about semantic intent.

Static analysis: "This variable is unused."
Verify: "This endpoint doesn't return what the spec says it should."

Both are useful. They serve different purposes. Verify does not replace your linter.

#### vs. Tests

Tests verify that code behaves correctly under specific inputs. Verify checks that the implementation appears to satisfy declared criteria.

Tests: "When I call getUser(123), it returns the right user."
Verify: "The getUser endpoint satisfies the criteria in the spec."

Tests catch behavior bugs. Verify catches spec violations. They complement each other.

#### vs. AI code review

AI code review tools generate comments on diffs from prompts that don't know the intended behavior. Verify starts from declared criteria and checks against them.

Both use AI. The difference is the contract: AI code review has no contract, so its judgements are unanchored. Verify has the spec, so judgements are anchored to declared intent.

### See also

* [Reference: Verification results](../reference/understanding-verification-results.md)
* [How to fix failures](../how-to-guides/fixing-verification-failures.md)
* [Verification layers](verification-layers.md)
