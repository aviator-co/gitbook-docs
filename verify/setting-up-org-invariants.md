# Setting up org invariants

Invariants are rules that apply to every matching change. Instead of asking the agent to assert "requires authentication" on every submission, you encode it once. Every matching runbook is then checked automatically.

In this tutorial, you'll create a security invariant by hand, watch the selector pick it up, and learn how it appears in a real verification run.

**Time:** ~10 minutes

**Prerequisites:**

* Admin access to your Aviator account.
* At least one repository connected to Verify.
* Familiar with [How Verify works](how-it-works.md) and [Concepts: Invariants](concepts/invariants.md).

### Step 1: Open the invariant editor

Go to **Verify → Settings → Invariants**.

You'll see the invariant catalog — every entry your account has, grouped by category. The list may already include AI-drafted entries (from PR-comment mining or docs extraction) and template-derived ones; those wait in draft status until an admin promotes them.

For this tutorial, you'll create a manual entry.

### Step 2: Create a new invariant

Click **New invariant** and give it:

* **Title:** `auth-required-on-handlers`
* **Category:** `security`
* **Source:** `manual` (set automatically when you create from the UI)

Pick a title you'll be okay with seeing in the audit trail — every verdict references it.

### Step 3: Write the body

The body is the assertion the verifier will check. Keep it specific about the assertion, vague about the implementation:

```
All HTTP handlers must call an authentication middleware before any
business logic. Endpoints that intentionally accept anonymous traffic
must declare themselves as exceptions (see Conditions below).
```

A few rules (covered in depth in [Invariants — Writing a good invariant](concepts/invariants.md#writing-a-good-invariant)):

* Don't name specific functions or modules — the rule should survive renames.
* Don't write the *fix* — the verifier will explain what's wrong.
* If you need "do X *except* when Y," use conditions or accept that the selector will pass on Y-shaped runbooks.

### Step 4: Add conditions (optional)

Conditions gate when the invariant is *eligible* to apply. Most invariants don't need them — leave them empty and the invariant is eligible for every runbook.

For this security rule, add a condition if you want to scope by language:

* **Condition type:** `file_path_glob`
* **Pattern:** `src/**/*.go`

This makes the invariant eligible only when a runbook touches Go files under `src/`.

Eligibility doesn't mean the invariant applies — the selector still decides per-runbook whether it's a defensible match. Conditions are useful for hard exclusions, not fine-grained scoping.

### Step 5: Save as draft

Click **Save**. New invariants land in **draft** status.

Draft invariants are visible in the catalog but the selector ignores them when building runbook criterion lists. Use the draft state to review the rule's wording — both with yourself and with the rest of your team — before it starts producing verdicts.

### Step 6: Promote to active

Once you're happy with the wording, edit the invariant and toggle **Status → Active**.

From this point on, the selector considers this invariant for every new runbook. When it picks the invariant for a runbook, it materializes it as an acceptance criterion (tagged with `source: baseline_invariant`) and the criterion is verified through the standard pipeline.

The audit trail records the promotion: who promoted, when, and from which draft state.

### Step 7: Watch it in a real runbook

Trigger a verification on a PR that touches Go files under `src/`. Open the review document.

If the selector picked your invariant for this change, you'll see a criterion in the criterion list tagged as an invariant. It runs alongside the user-supplied criteria. Verdict + evidence are produced the same way.

If the selector *didn't* pick it, that's fine — the LLM judged it didn't apply to this change. Look at the runbook's spec and scope: does the rule actually apply here? If yes and the selector missed it, the rule body may be too vague.

### Step 8: See a violation

Push a change that intentionally violates the rule — for example, a handler that skips the middleware:

```go
func (h *Handler) PublicEndpoint(w http.ResponseWriter, r *http.Request) {
    // No auth middleware called
    w.Write([]byte("hello"))
}
```

The next verification run shows the invariant criterion as failed:

```
✗ auth-required-on-handlers (invariant)
  Handler PublicEndpoint at src/handlers/public.go:23 does not call an
  authentication middleware before responding.
```

The verdict explains the failure but doesn't prescribe the fix — that's deliberate.

### Step 9: Waive when appropriate

If a verdict is wrong or doesn't apply, the reviewer can waive it from the review document with a categorized reason: `false_positive`, `doesnt_apply`, `accepted_risk`, or `fix_in_followup`. Every waiver is recorded in the audit trail.

If you find yourself waiving the same invariant repeatedly, the rule is wrong. See [Concepts: Invariants — Waivers](concepts/invariants.md#waivers).

### What you just did

* Created a manual invariant in the account catalog.
* Wrote a rule body that's specific about the assertion but doesn't prescribe implementation.
* Added a glob condition to scope eligibility by language.
* Promoted it from draft to active, at which point the selector started considering it.
* Saw it materialize as a criterion in a real runbook and produce a verdict.

### Next steps

* [Concepts: Invariants](concepts/invariants.md) — sources (including AI-from-PR-comments), conditions, categories, waivers.
* [Verification layers](concepts/verification-layers.md) — how invariants compose with user criteria.
* [Fixing verification failures](how-to-guides/fixing-verification-failures.md) — what reviewers do when an invariant verdict goes red.
