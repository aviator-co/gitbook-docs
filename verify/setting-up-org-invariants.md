# Setting up baseline invariants

Baseline invariants are repo-wide rules that apply to many changes — "all HTTP handlers require auth," "no hardcoded secrets," "all GraphQL mutations log to the audit table." Rather than restating them in every spec, you define them once on the repository and Verify composes them into the criteria for any PR they apply to.

This tutorial walks through the onboarding flow that creates your initial set of invariants and shows how they appear in verification results.

**Time:** ~10–20 minutes (depending on questionnaire time)

**Prerequisites:**

* Admin access to your Aviator organization
* A repository connected for Verify (see [Connect a repository](how-to-guides/connect-a-repository.md))

### How invariants are created

Invariants are seeded from a guided onboarding session, not authored from scratch in a settings page. The session:

1. Analyzes your repository
2. Asks you a few questions about team practices and priorities
3. Drafts invariants based on the analysis, your answers, and signals in your repo (CLAUDE.md, CONTRIBUTING.md, etc.)
4. Asks you to review each draft

You can edit invariants after onboarding, but the LLM-drafted starting set is the on-ramp.

### Step 1: Start the onboarding session

Go to **Verify → Invariants** for your repository and click **Start setup**.

If onboarding has already been completed for this repo, you'll see your existing invariants instead. You can still add or edit invariants from this page.

### Step 2: Analyze the repository

The first stage scans your repo's structure and conventions. This is automatic; no input needed. While it runs, you can read about what's being analyzed.

When the analysis completes, you'll see a summary: languages detected, key directories, conventions found.

### Step 3: Answer the questionnaire

You'll be asked a short series of questions about your team's practices:

* What kinds of issues most often slip through code review?
* Which areas of the codebase carry the most risk?
* What rules are universal vs. domain-specific?

Your answers shape the drafts.

### Step 4: Review the drafted invariants

The session presents a set of drafted invariants. Each one has:

* **Name** — a short identifier
* **Rule text** — what the verifier will check
* **Conditions** — file paths, languages, or change types that determine when the invariant applies

For each draft, you can:

* **Approve** — the invariant becomes `ACTIVE` and starts applying to matching PRs
* **Edit** — refine the wording or conditions before approving
* **Reject** — discard the draft

You don't have to approve everything at once. You can come back and finish later.

### Step 5: See invariants in verification

When verification runs against a PR, applicable invariants are composed into the criteria set. They appear in the verification result alongside spec-specific criteria, with no special separation:

```
Verification run #142

✓ Endpoint: GET /api/v1/users      ← from spec
✓ Returns 200 with profile data    ← from spec
✓ All HTTP handlers use AuthMiddleware  ← from invariant
✓ No hardcoded credentials         ← from invariant
✓ SQL queries use parameterized statements  ← from invariant
```

If an invariant fails, the result includes the verifier's reason and a location:

```
✗ No hardcoded credentials
  Possible API key at src/client.go:23
```

### Editing invariants later

Go to **Verify → Invariants** to:

* Edit an existing invariant's rule text or conditions
* Archive an invariant that's no longer relevant
* Add a new invariant manually

A change to an invariant takes effect on the next verification run.

### Handling legitimate exceptions

Sometimes a change legitimately departs from an invariant — a public health-check endpoint may not need authentication, even though the auth invariant otherwise applies.

You have two options:

1. **Sharpen the invariant's conditions** so it doesn't apply to that file path. Best when the exception is recurring.
2. **Document the exception in the spec's Intent.** The verifier reads the full spec for context and can recognize an intentional override when the rationale is documented.

### What you learned

* Invariants are seeded by a guided onboarding session, not authored from scratch
* Each invariant has a rule, a description, and conditions for when it applies
* Invariants compose with per-spec criteria into a single criteria set per verification run
* You don't repeat invariant rules in individual specs
* Exceptions are handled either by sharpening conditions or by documenting the override in the spec

### Next steps

* [Verification layers](concepts/verification-layers.md) — How invariants and specs compose
* [Configuration reference](reference/configuration-reference.md) — Invariant fields and settings
