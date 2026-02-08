# Verification layers

Verify checks code against multiple layers of requirements. This document explains how those layers work together.

### The three layers

```
┌─────────────────────────────────────┐
│         Org Invariants              │  ← Always apply
├─────────────────────────────────────┤
│        Domain Contracts             │  ← Apply to specific areas
├─────────────────────────────────────┤
│      Acceptance Criteria            │  ← Specific to this change
└─────────────────────────────────────┘
```

Each layer serves a different purpose:

| Layer               | Scope              | Changes      | Purpose                         |
| ------------------- | ------------------ | ------------ | ------------------------------- |
| Org Invariants      | Organization-wide  | Rarely       | Security, standards, compliance |
| Domain Contracts    | Per module/service | Occasionally | Module-specific rules           |
| Acceptance Criteria | Per spec           | Every change | Task-specific requirements      |

### Org invariants

Org invariants are rules that apply to every change in your organization.

**Examples:**

* All HTTP handlers must use authentication middleware
* No hardcoded credentials, API keys, or tokens
* All external input must be validated
* SQL queries must use parameterized statements
* Errors must include correlation IDs

These rules are defined once and checked automatically. Developers don’t need to include them in specs—they’re always enforced.

#### Why org invariants matter

Some requirements are universal. Every endpoint should require authentication (except explicit exceptions). No code should contain hardcoded secrets. These aren’t negotiable per-feature.

Without org invariants, you’d need to add “requires authentication” to every spec. People would forget. Invariants make these checks automatic.

#### Configuring org invariants

Invariants are configured by admins in **Verify → Settings → Org Invariants**.

They’re written in natural language:

```markdown
## Security Baseline

### Authentication
-All HTTP handlers must use AuthMiddleware
-No endpoint may bypass authentication without exception

### Secrets
-No hardcoded credentials, API keys, or tokens
-Secrets must come from environment variables

### Exceptions
-Health check endpoints (/health, /ready, /live)
```

Invariants can have exceptions for legitimate edge cases.

### Domain contracts

Domain contracts are rules that apply to specific parts of your codebase.

**Examples:**

* Billing module: Never modifies user records directly
* Payments domain: All amounts use the Money type
* User data: All reads go through the UserRepository

Domain contracts are more specific than org invariants but more general than per-spec criteria.

#### Why domain contracts matter

Different parts of your codebase have different rules. The billing module shouldn’t directly modify user records—it should emit events that the user service handles. The payments domain should always use a Money type to avoid floating-point errors.

These rules apply to any change in that domain, regardless of what the specific spec says.

#### Configuring domain contracts

Domain contracts are associated with file paths:

```yaml
name: billing-domain
applies_to:"src/billing/**"
rules:|
  ## Billing Domain Rules
  - Never modify user records directly
  - All amounts use Money type
  - Emit events for all state changes
  - All external calls go through BillingClient
```

When a change touches files in `src/billing/**`, these rules are checked.

### Acceptance criteria

Acceptance criteria are requirements specific to a single change. They’re defined in the spec and apply only to that spec.

**Examples:**

* Endpoint: `GET /api/v1/subscription/status`
* Response includes: status, renewal\_date, plan\_name
* Response excludes: internal\_id
* Returns 404 if subscription not found

#### Why acceptance criteria matter

Org invariants and domain contracts capture recurring rules. But each change has unique requirements. The subscription endpoint needs to return specific fields. A refactoring needs to preserve existing behavior.

Acceptance criteria capture what’s unique to this change.

### How layers combine

When verification runs, all applicable layers are checked:

1. **Spec criteria** — Always checked (defined in spec)
2. **Domain contracts** — Checked if change touches relevant files
3. **Org invariants** — Checked if change touches relevant patterns

All layers must pass for verification to pass.

#### Example

Spec:

```markdown
# Add subscription status endpoint

## Acceptance Criteria
-[ ] Endpoint: GET /api/v1/subscription/status
-[ ] Response includes: status, renewal_date
```

Domain contract (billing-domain):

```
- All amounts use Money type
```

Org invariant (security-baseline):

```
- All HTTP handlers must use AuthMiddleware
```

Verification checks:

* ✓ Endpoint exists at correct path (spec)
* ✓ Response includes status, renewal\_date (spec)
* ✓ Any amounts use Money type (domain)
* ✓ Handler uses AuthMiddleware (org)

All four must pass.

### Inheritance and override

Higher layers can be overridden by lower layers with explicit justification.

If your spec says:

```markdown
## Intent
Public health check endpoint. Must be accessible without authentication
for load balancer integration.

## Acceptance Criteria
-[ ] Does not require authentication
```

The spec explicitly overrides the org invariant requiring authentication. Verification understands the exception is intentional and documented.

But this only works when:

* The spec Intent explains why
* The override is explicit
* There’s no `forbid` on auth bypass in invariants

### When to use each layer

| Use case                              | Layer               |
| ------------------------------------- | ------------------- |
| Rules that always apply everywhere    | Org invariants      |
| Rules specific to a module or service | Domain contracts    |
| Requirements for a specific change    | Acceptance criteria |

Don’t over-specify at higher layers. If a rule only matters sometimes, it’s not an invariant—it’s a criterion for specs that need it.

### See also

* [Setting up org invariants](verification-layers.md#org-invariants)
* [How verification works](how-verification-works.md)
* [Configuration options](../reference/configuration-reference.md)
