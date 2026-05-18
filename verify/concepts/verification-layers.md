# Verification layers

Verify composes two sources of requirements when it checks a PR:

```
┌─────────────────────────────────────┐
│      Baseline invariants            │  ← repo-wide standing rules
├─────────────────────────────────────┤
│      Acceptance criteria            │  ← specific to this spec
└─────────────────────────────────────┘
```

Each layer serves a different purpose:

| Layer               | Scope              | Changes      | Purpose                            |
| ------------------- | ------------------ | ------------ | ---------------------------------- |
| Baseline invariants | Repository-wide    | Rarely       | Standing rules across all changes  |
| Acceptance criteria | Per spec           | Every change | Task-specific requirements         |

### Baseline invariants

Baseline invariants are rules that apply to many changes in a repository.

**Examples:**

* All HTTP handlers must use authentication middleware
* No hardcoded credentials, API keys, or tokens
* All external input must be validated
* SQL queries must use parameterized statements
* Errors must include correlation IDs
* All GraphQL mutations must log to the audit table

Invariants are defined once on the repository. When you write a spec, you don't repeat them — they apply automatically to any PR whose files match the invariant's conditions.

#### How invariants are seeded

When you onboard a repository, Verify analyzes existing signals and proposes an initial set of invariants:

* CLAUDE.md and similar AI-instruction files
* CONTRIBUTING.md and project conventions
* Architectural patterns discoverable from the code

You review the proposed invariants and approve or edit them before they go live. The set grows over time as your team identifies new things worth checking.

#### Invariant conditions

Each invariant has conditions that determine which PRs it applies to:

* File-path globs (e.g., `src/handlers/**`)
* Languages (e.g., Python, TypeScript)
* Change types (e.g., new files, modifications)

When a PR is pushed, Verify composes the set of invariants whose conditions match the changed files and merges them into the acceptance criteria for that run.

#### Configuring invariants

Invariants are managed through the dashboard at **Verify → Invariants**.

Each invariant has:

| Field                 | Description                                  |
| --------------------- | -------------------------------------------- |
| **Name**              | Short identifier                             |
| **Description**       | Plain-language description of the rule       |
| **Conditions**        | File paths, languages, or change types       |
| **Rule text**         | The rule as evaluated by the verifier        |

Rules are written in natural language and interpreted by the verifier.

### Acceptance criteria

Acceptance criteria are requirements specific to a single change. They are defined in the spec and apply only to that spec.

**Examples:**

* Endpoint: `GET /api/v1/subscription/status`
* Response includes: status, renewal_date, plan_name
* Response excludes: internal_id
* Returns 404 if subscription not found

#### Why acceptance criteria matter

Baseline invariants capture recurring rules. But each change has unique requirements. The subscription endpoint needs to return specific fields. A refactoring needs to preserve existing behavior.

Acceptance criteria capture what's unique to this change.

### How the layers combine

When verification runs, the engine:

1. Loads the spec's current acceptance criteria
2. Composes baseline invariants whose conditions match the changed files
3. Merges both into a single set of items for the verifier
4. Reports a unified per-item verdict

From the verifier's perspective there are no separate "layers" — the verifier sees one list of criteria. The layering exists at the authoring level: criteria are written once per spec, invariants are written once per repo.

#### Example

Spec:

```markdown
# Add subscription status endpoint

## Acceptance Criteria
- [ ] Endpoint: GET /api/v1/subscription/status
- [ ] Response includes: status, renewal_date
```

Repo baseline invariant whose conditions match `src/handlers/**`:

```
All HTTP handlers must use AuthMiddleware
```

The verifier runs against three items:

* Endpoint exists at correct path (from spec)
* Response includes status, renewal_date (from spec)
* Handler uses AuthMiddleware (from invariant)

All three must pass for verification to pass overall.

### When invariants would conflict with a spec

Sometimes a spec describes a change that legitimately departs from an invariant. A public health-check endpoint may not need authentication, even though the auth invariant otherwise applies.

The verifier reads the spec's Intent and acceptance criteria as the source of truth for the specific change. If the spec explicitly says "Does not require authentication," the verifier can recognize the exception in context.

If the override is intentional, document it in the spec's Intent so the verifier — and future reviewers — understand why.

### When to use each layer

| Use case                              | Layer               |
| ------------------------------------- | ------------------- |
| Rules that apply across many changes  | Baseline invariants |
| Requirements specific to one change   | Acceptance criteria |

Don't over-specify at the invariant layer. If a rule only matters sometimes, it's not an invariant — it's a criterion that belongs in the specs that need it.

### See also

* [Tutorial: Setting up baseline invariants](../setting-up-org-invariants.md)
* [How verification works](how-verification-works.md)
