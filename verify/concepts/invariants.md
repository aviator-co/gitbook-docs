# Invariants

An **invariant** is a team-defined rule that Verify applies to every matching change. Where user-supplied acceptance criteria describe what *this* change should do, invariants describe what *every* change should respect. They live in your Aviator account and update once for everyone.

A good invariant captures something your team learned the hard way — usually as a recurring review comment — so reviewers don't have to flag it again.

### Where invariants come from

Invariants live in a per-account catalog. Each invariant has a source:

| Source                  | What it is                                                                                    |
| ----------------------- | --------------------------------------------------------------------------------------------- |
| **Manual**              | Authored by an admin in **Settings → Invariants**.                                           |
| **Template**            | Instantiated from Aviator's starter library — common patterns you can adopt as-is or tweak. |
| **AI-generated**        | AI-drafted from signals about the repo, awaiting admin approval.                             |
| **AI from docs**        | Extracted from your repo's `CONTRIBUTING.md`, `LLM.md`, or similar files by the docs pipeline. |
| **AI from PR comments** | Mined from your team's recurring PR-review comments. Often the highest-yield source — it captures real-world feedback your team has already given. |

Sources that aren't `manual` produce drafts. Admins promote drafts to active in the UI; that's when they start producing verdicts.

### Conditions

Each invariant has zero or more **conditions** that gate when it's eligible to apply:

| Condition type   | What it matches                                                |
| ---------------- | -------------------------------------------------------------- |
| `file_path_glob` | Files changed in the runbook match a glob like `src/**/*.py`. |
| `language`       | The detected language of changed files matches.                |

An invariant with no conditions is eligible for every runbook. With conditions, it's only eligible when at least one matches the change.

Eligibility doesn't mean the invariant *applies* — it just means the next step (selection) will consider it.

### Categories

Every invariant belongs to a category. Default categories:

| Category                    | What it covers                                                                |
| --------------------------- | ----------------------------------------------------------------------------- |
| `functional_correctness`    | The change behaves correctly for expected and edge-case inputs.               |
| `test_coverage`             | New and changed code is covered by meaningful tests.                          |
| `security`                  | The change avoids common vulnerabilities and enforces required controls.     |
| `performance`               | The change doesn't introduce avoidable latency, memory, or query regressions. |
| `accessibility`             | UI changes meet accessibility standards.                                      |
| `observability`             | Errors emit metrics; logging uses structured fields.                          |
| `backwards_compatibility`   | Public surfaces don't break existing consumers.                               |
| `documentation`             | Public APIs and behavior changes are documented.                              |
| `code_style`                | Code follows team conventions (type usage, naming, layering).                |

Categories drive grouping in the UI and reporting in the audit trail.

### How invariants apply to a runbook

When a runbook is created, a **selector** picks which eligible invariants actually apply. The selector reads the runbook's spec, the generated plan, and the scope and uses an LLM to pick the catalog entries that defensibly fit the change.

Selected invariants are materialized as acceptance criteria on the runbook, tagged with `source: baseline_invariant`. From that point on, they flow through the [verification pipeline](how-verification-works.md) like any other criterion — same verdict shape, same evidence, same review-document treatment.

Two consequences:

* **You don't need to think about invariants when writing a spec.** The selector handles eligibility. Your spec's criteria stay focused on what's specific to this change.
* **Invariant verdicts and user-criterion verdicts look identical in the review document.** The only visible difference is the source tag, and the fact that invariant criteria can't be edited per-runbook (they can be waived).

### Writing a good invariant

Three rules of thumb:

**Be specific about the assertion, vague about the implementation.**

* ✓ "All HTTP handlers must call an authentication middleware before any business logic."
* ✗ "Use `AuthMiddleware` from `src/auth/middleware.go`." (Brittle — the check should survive renames and module moves.)

**Make the rule verifiable in isolation.**

A rule that requires running the whole system is hard to verify. Prefer rules that can be checked from the diff or from a single runtime probe.

* ✓ "All migrations must declare a `down` block."
* ✗ "All migrations must be reversible." (Can't be checked without running them backwards.)

**Use conditions sparingly.**

The selector reads the runbook context and picks what fits. Don't over-restrict with conditions — let the selector pass on inapplicable cases. Conditions are useful for hard exclusions (e.g. language-specific rules), not for fine-grained scoping.

### Turning a review comment into an invariant

Most invariants worth writing start as a review comment that's been left more than twice. The mining pipeline — `ai_generated_pr_comments` — does this automatically by reading your PR history, but you can also do it by hand:

1. **Find the recurring comment.** Scan PRs over the last quarter.
2. **Write the assertion.** State the rule in one sentence. Don't write the *fix* — the verifier will explain what's wrong.
3. **Pick a category.** Helps with grouping and reporting.
4. **Add conditions if needed.** Most invariants don't need them.
5. **Save as draft, watch a week of verifications.** Draft invariants don't get materialized into runbooks. Use that to confirm the rule reads cleanly before promoting to active.

Example, turning a real review comment into an invariant:

> Comment on PR #4173: "Please don't write to `users` directly — go through `UserRepository.UpdateProfile`. We had a partial-write bug last quarter from a similar pattern."

Invariant body:

```
Writes to the users table must go through UserRepository. Direct INSERT,
UPDATE, or DELETE statements against the users table are not allowed
outside the repository package. Schema migrations under src/db/migrations
are exempt.
```

Conditions: `file_path_glob: src/**/*.go` (skip non-Go files).

Category: `functional_correctness`.

### Waivers

Invariant verdicts can be waived from the review document with a categorized reason:

| Waiver category    | When to use it                                                                 |
| ------------------ | ------------------------------------------------------------------------------ |
| `false_positive`   | The invariant fired but the rule misjudged this case.                          |
| `doesnt_apply`     | The rule is valid in general but isn't relevant to this PR.                    |
| `accepted_risk`    | The failure is real but the PR author accepts the trade-off.                   |
| `fix_in_followup`  | The failure is real and will be addressed in a separate follow-up PR.          |

Every waiver is recorded in the audit trail with the reviewer, the category, and the free-text reason. If you find yourself waiving the same invariant repeatedly, the rule is wrong — tighten its conditions, rephrase the body, or rebuild the rule from real review comments.

### See also

* [Setting up org invariants](../setting-up-org-invariants.md) — step-by-step setup
* [Verification layers](verification-layers.md) — how invariants compose with criteria in a run
* [How verification works](how-verification-works.md) — the verifier pipeline
* [How to: Writing a SKILL.md](../how-to-guides/writing-a-skill-md.md) — for runtime context, not for rules
