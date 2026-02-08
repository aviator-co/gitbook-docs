# How verification works

This document explains what happens when Verify checks code against a spec.

### The verification pipeline

When you push code to a branch with a linked spec, verification runs through several stages:

```
Spec Resolution → Scope Check → Org Invariants → Acceptance Criteria → Results
```

Each stage can pass or fail independently.

### Stage 1: Spec resolution

Verify identifies which approved spec applies to this branch.

It looks for:

1. A spec explicitly linked to this PR
2. A spec ID in the PR description
3. A spec matching the branch name pattern

If no approved spec is found, verification fails with “No approved spec found.”

Specs must be approved to trigger verification. Draft or in-review specs don’t count.

### Stage 2: Scope check

Before analyzing code semantically, Verify checks that the implementation stays within declared scope.

It compares files in the PR against the spec:

* Files in `modify` list → allowed
* Files matching `forbid` patterns → violation
* Files not in either → violation

Scope checking is fast and happens first because there’s no point doing deeper analysis if the change touched things it shouldn’t.

#### Why scope matters

Scope prevents drift. Without it, a “small fix” could touch files across the codebase. Explicit scope keeps changes contained.

Scope also catches accidental changes—files modified by IDE refactoring, merge conflicts, or copy-paste errors.

### Stage 3: Org invariants

Verify loads organization-wide rules that apply based on the files touched.

Invariants are configured separately from specs. They represent rules that always apply:

* Security requirements
* Coding standards
* Architecture constraints

The verifier determines which invariants are relevant. If your change modifies HTTP handlers, authentication invariants apply. If your change only touches tests, they might not.

Each invariant rule is checked against the implementation.

### Stage 4: Acceptance criteria

Each criterion from the spec is verified against the implementation.

This is the core of verification. The verifier analyzes the code semantically to determine whether each requirement is satisfied.

#### Semantic analysis

Verification doesn’t use pattern matching or static analysis alone. It uses AI to understand what the code actually does.

For example, if a criterion says “Response excludes internal\_id”:

1. Find what the endpoint returns
2. Trace data flow through transformations
3. Check whether `internal_id` could appear in the response

This catches issues that simple text search would miss:

* Fields added through object spread
* Data flowing through helper functions
* Conditional logic that might include forbidden fields

#### Criterion results

Each criterion gets a result:

* **Pass** — Implementation satisfies the requirement
* **Fail** — Implementation violates the requirement (with explanation)
* **Inconclusive** — Could not determine (rare edge case)

Failed criteria include:

* The reason for failure
* File and line number
* Code snippet
* Suggested fix (when possible)

### Results compilation

After all stages complete, results are compiled:

```json
{
  "status": "failed",
  "scope": { "status": "passed" },
  "org_invariants": { "status": "passed", "checked": 12 },
  "acceptance_criteria": { "status": "failed", "passed": 5, "failed": 2 }
}
```

Results are:

* Posted to GitHub as a PR check
* Stored in the Aviator dashboard
* Recorded in the audit trail
* Sent via notifications (if configured)

### Determinism

Verification is deterministic. Running it twice on the same code and spec produces the same result.

This matters because:

* You can reproduce failures
* Re-running after no changes doesn’t flip results
* Audit records are reliable

### Performance

Verification typically completes in under 2 minutes. Factors that affect speed:

| Factor                 | Impact                                                         |
| ---------------------- | -------------------------------------------------------------- |
| Diff size              | Larger diffs take longer                                       |
| Number of criteria     | More criteria = more checks                                    |
| Complexity of criteria | “Requires auth” is faster than “All queries are parameterized” |
| Org invariant count    | More invariants = more checks                                  |

If verification times out, consider:

* Breaking large changes into smaller specs
* Simplifying overly broad criteria
* Checking for unusual code patterns

### Comparison with other approaches

#### vs. Static analysis

Static analysis tools (linters, type checkers) verify syntax and patterns. Verification checks semantic intent.

Static analysis: “This variable is unused.” Verification: “This endpoint doesn’t return what the spec says it should.”

Both are useful. They serve different purposes.

#### vs. Tests

Tests verify that code behaves correctly under specific inputs. Verification checks that code matches a spec.

Tests: “When I call getUser(123), it returns the right user.” Verification: “The getUser endpoint satisfies all the requirements in the spec.”

Tests and verification complement each other. Tests catch bugs; verification catches spec violations.

#### vs. AI code review

AI code review tools generate comments on diffs. They’re faster than human review but have similar limitations:

* They don’t know the intended behavior
* Comments don’t create audit trails
* It’s still sampling, not systematic checking

Verification starts from declared intent (the spec) and checks systematically.

### See also

* [Reference: Verification results](../reference/understanding-verification-results.md)
* [How to fix failures](../how-to-guides/fixing-verification-failures.md)
* [Verification layers](verification-layers.md)
