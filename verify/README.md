# Verify

Aviator Verify replaces traditional code review with spec-driven verification. Instead of reviewing code line-by-line, your team approves structured specs that define what the code should do. Verify then automatically checks that implementations match approved specs. These verifications also create a compliance audit trail.

### How it works

1. **Write a spec.** Describe what you want to build. The AI assistant helps generate a structured spec with intent, scope, and acceptance criteria.
2. **Get it approved.** Submit the spec for review, similar to a code review. A reviewer approves the intent and requirements, not the code.
3. **Implement.** Write the code using any tool: Cursor, Copilot, Claude, or manually. The approved spec is your contract.
4. **Verify.** Push your code. Verify automatically checks that the implementation satisfies every criterion in the approved spec.
5. **Merge.** If verification passes, merge with confidence. You have a complete audit trail linking intent → approval → implementation → proof.

### Why this approach

Code review was designed when humans wrote code slowly. AI changes that equation. Code can be generated in minutes, but reviewing it still takes just as long.

Traditional review asks: "Does this code look okay?"

Verify asks: **"Does this code do what we agreed it should do?"**

This shift has several advantages:

* **Faster reviews.** Approving a spec takes minutes. You're agreeing on _what_, not auditing _how_.
* **Systematic checking.** Every criterion is verified, every time. No sampling, no fatigue.
* **Complete audit trails.** Every change links to approved intent, verification results, and who approved what.

→ Why spec-driven verification

### Core concepts

#### Specs

A spec defines what a change should accomplish. It has three sections:

| Section                 | Purpose                                    |
| ----------------------- | ------------------------------------------ |
| **Intent**              | Plain-language description of what and why |
| **Scope**               | Files and services the change may touch    |
| **Acceptance Criteria** | Specific, verifiable requirements          |
| **Execution steps**     | The implementation details                 |

```markdown
# Add subscription status endpoint

## Intent
Authenticated endpoint for retrieving user subscription status
without exposing internal billing identifiers.

## Scope
- **modify:** `src/handlers/subscription.go`, `src/models/subscription.go`
- **call:** `subscription-service`
- **forbid:** `src/auth/*`, database migrations`

## Acceptance Criteria
- [ ] Endpoint: `GET /api/v1/subscription/status`
- [ ] Requires authentication
- [ ] Response includes: status, renewal_date, plan_name
- [ ] Response excludes: internal_id, billing_provider_id
- [ ] Returns 404 if subscription not found`

# Execution Steps

### Step 1: Define the response model
Create a model that only includes the allowed fields.

#### 1.1: Create UserProfile struct
Add a new struct in the models package that excludes sensitive fields.

- Create `UserProfile` struct in `src/models/user.go`
- Include only `display_name` and `avatar_url` fields
- Add JSON tags for serialization

### Step 2: Implement the handler
Create the HTTP handler that fetches and returns profile data.

#### 2.1: Create GetProfile handler
Add a handler function that retrieves user data and returns the public profile.

- Create `GetProfile` function in `src/handlers/profile.go`
- Parse user ID from URL path
- Call `user-service` to fetch user data

#### 2.2: Map to response model
Convert the internal user object to the public profile response.

- Map user fields to `UserProfile` struct
- Ensure `email` and `internal_id` are not included

```

Specs are created with AI assistance, reviewed by humans, and locked once approved. Any changes to the spec require a re-review.

→ Tutorial: Your first spec

→ Reference: Spec format

#### Verification

When you push code, Verify checks it against the approved spec:

1. **Scope check** — Did you only modify declared files?
2. **Criteria check** — Does the implementation satisfy each requirement?
3. **Invariants check** — Does it follow organization-wide rules?

Results appear as a GitHub PR check. Pass means you're clear to merge. Fail shows exactly what went wrong and where.

→ Explanation: How verification works

→ Reference: Verification results

#### Verification layers

Verify checks against multiple layers of requirements:

| Layer                   | Scope            | Purpose                         |
| ----------------------- | ---------------- | ------------------------------- |
| **Org Invariants**      | All changes      | Security, standards, compliance |
| **Domain Contracts**    | Specific modules | Module-specific rules           |
| **Acceptance Criteria** | Single spec      | Task-specific requirements      |

Org invariants apply automatically—you don't need to add "requires authentication" to every spec. Define it once, enforce everywhere.

→ [Tutorial: Setting up org invariants](setting-up-org-invariants.md)

→ [Explanation: Verification layers](./#verification-layers)

#### Audit trails

Every action creates an immutable record:

* Spec created, by whom, when
* Spec approved, by whom, when
* Verification run, results, what was checked
* Links to commits, PRs, tickets

This provides complete traceability for compliance. If an auditor asks "why was this change made?", you can show the approved spec, who authorized it, and proof the implementation matched.

→ Explanation: Audit trails and compliance

→ How-to: Export audit logs

### Getting started

The fastest way to understand Verify is to use it:

1. [Tutorial: Your first spec](your-first-spec.md) - Create a spec, get it approved, see verification in action (15 min)
2. [How-to: Connect a repository](how-to-guides/connect-a-repository.md) - Set up GitHub integration
3. [How-to: Configure branch protection](how-to-guides/configuring-branch-protection.md) - Require verification before merge

### Quick links

* New to Verify? Start with [Your first spec](your-first-spec.md)
* Setting up? See [Connect a repository](how-to-guides/connect-a-repository.md)
* Verification failed? Check [Fix verification failures](how-to-guides/fixing-verification-failures.md)
* Need compliance info? Read [Audit trails and compliance](concepts/audit-trails-and-compliance.md)
