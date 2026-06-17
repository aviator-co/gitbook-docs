# Setting up org invariants

Org invariants are rules that apply to every change in your organization. Instead of asking the agent to assert "requires authentication" on every change, you encode it once. Every matching change is then checked automatically.

In this tutorial, you'll create a security invariant that enforces authentication on HTTP handlers, watch it apply to a real change, and learn how to declare an exception.

**Time:** ~10 minutes

**Prerequisites:**

* Admin access to your Aviator organization
* At least one repository connected to Verify
* Familiar with [How Verify works](how-it-works.md) and [Concepts: Invariants](concepts/invariants.md)

### Step 1: Open the invariant editor

Go to **Verify → Settings → Invariants → Org-level**.

You'll see a list of existing invariants (possibly empty) and a button to create a new one.

### Step 2: Create a new invariant

Click **New invariant** and give it:

* **Name:** `security-baseline`
* **Description:** *Core security requirements for all code changes*

Pick a name you'll be okay with in the audit trail — every verdict references it.

### Step 3: Write the rule

The rule is the assertion the verifier will check. Keep it specific about the assertion, vague about the implementation.

```
All HTTP handlers must call an authentication middleware before any
business logic. Endpoints that intentionally accept anonymous traffic
must declare themselves as exceptions (see below).
```

A few rules of thumb (covered in depth in [Invariants — Writing a good invariant](concepts/invariants.md#writing-a-good-invariant)):

* Don't name specific functions or modules — the rule should survive renames.
* Don't write the *fix* — the verifier will explain what's wrong.
* If you need "do X *except* when Y," declare Y as an exception, not as a separate rule.

### Step 4: Set the scope

Org invariants apply everywhere by default. For some rules, you may want to narrow:

* **Applies to:** `All files` — the default. Right for security baselines and observability rules.
* **Applies to (glob):** `src/**/*.go` — narrower. Use when the rule is language-specific.
* **Exclude:** `tests/**`, `**/migrations/**` — common for rules that shouldn't fire on test code or generated code.

For the security baseline, leave **Applies to** as `All files`.

### Step 5: Declare exceptions

Every real rule has exceptions. Authentication usually has a few legitimate ones — health checks, webhook receivers (which validate by signature instead), metrics endpoints.

Add them in the **Exceptions** field:

```
- Health endpoints: /health, /ready, /live
- Metrics endpoint: /metrics
- Webhook receivers under /webhooks/* (validated by signature)
```

Declaring exceptions here is what keeps your audit trail clean. Without them, every webhook endpoint produces a verdict the reviewer has to manually waive — that noise erodes trust in invariants.

### Step 6: Save as draft

Click **Save as draft**.

Draft invariants produce verdicts in the review document but don't block merge. Use the draft state to confirm the rule fires only where you expect — usually for a week or two of real verification traffic.

### Step 7: Watch it apply to a real change

Open a recent PR that's already been verified. You should see the new invariant in the review document under the criterion list:

```
✓ security-baseline (org invariant)
  → matched: all HTTP handlers in this change use AuthMiddleware
```

If the invariant fired but you expected it not to, that's the signal to tighten its scope. If it didn't fire but you expected it to, broaden the scope.

For draft invariants, the verdict is annotated **Draft** so reviewers know it doesn't block.

### Step 8: See what a violation looks like

Push a small test change that violates the rule — for example, a handler that skips the middleware:

```go
func (h *Handler) PublicEndpoint(w http.ResponseWriter, r *http.Request) {
    // No auth middleware called
    w.Write([]byte("hello"))
}
```

The review document will now show:

```
✗ security-baseline (org invariant)  Draft

  Handler PublicEndpoint does not call an authentication middleware.
  Location: src/handlers/public.go:23

  This invariant requires all HTTP handlers to call AuthMiddleware
  unless declared as an exception. If this endpoint should be public,
  add it to the exceptions list on the invariant.
```

The verdict explains *what's wrong* and points at the fix path — but the rule itself stayed vague about *how* (no mention of `AuthMiddleware` import paths or specific function names).

### Step 9: Promote to enforcing

Once you're confident the rule fires correctly, edit the invariant and toggle **Status: Enforcing**. From the next run onward, violations of this invariant block merge unless the reviewer waives them with a reason.

The audit trail records the promotion: who promoted, when, and from which draft state.

### What you just did

* Created an org-level invariant that applies to every change.
* Wrote a rule that's specific about the assertion but doesn't prescribe implementation.
* Declared exceptions explicitly, so legitimate cases don't generate noise.
* Shipped it as draft, watched a few runs, then promoted to enforcing.

### Next steps

* [Concepts: Invariants](concepts/invariants.md) — scope precedence, categories, anti-patterns, turning a review comment into an invariant.
* [Verification layers](concepts/verification-layers.md) — how invariants compose with acceptance criteria in a run.
* [Fixing verification failures](how-to-guides/fixing-verification-failures.md) — what reviewers do when an invariant verdict goes red.
