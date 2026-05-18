# Spec format

Reference for Verify spec syntax.

### Structure

A spec is a markdown document. The Verify parser recognizes two structured sections by heading:

* `## Scope`
* `## Acceptance Criteria`

Other sections — Intent, Execution Steps, Design Notes, etc. — are not parsed but are useful for human reviewers and AI implementation tools. A common, recommended layout is:

```markdown
# Title

## Intent
[plain-language description of what and why]

## Scope
[scope declarations]

## Acceptance Criteria
[list of criteria]

## Execution Steps
[implementation breakdown]
```

### Title

Short description of the change. Appears in the dashboard and PR check.

```markdown
# Add user profile endpoint
```

### Intent (not parsed)

Plain-language description of what and why. The verifier reads the full spec for context, so this is useful to include even though it's not a parsed section.

```markdown
## Intent
Public endpoint for retrieving basic user profile information.
Returns display name and avatar, but not email or internal IDs.
```

### Scope (parsed)

Declares what the change may touch using three keywords:

| Keyword  | Purpose                     | Required |
| -------- | --------------------------- | -------- |
| `modify` | Files to create or edit     | No       |
| `call`   | External services to access | No       |
| `forbid` | Prohibited files or actions | No       |

#### Syntax

```markdown
## Scope
- **modify:** `src/handlers/profile.go`, `src/models/user.go`
- **call:** `user-service`
- **forbid:** `src/auth/*`, database migrations
```

Scope is currently used by the verifier as context for its judgement, but is not enforced as a separate hard gate. If the implementation modifies a file outside `modify`, the verifier may flag this in its reasoning; it will not block verification before the LLM stage.

#### Glob patterns

| Pattern                | Matches                                      |
| ---------------------- | -------------------------------------------- |
| `src/handlers/*.go`    | .go files in src/handlers                    |
| `src/handlers/**/*.go` | .go files in src/handlers and subdirectories |
| `tests/*_test.go`      | Files ending in `_test.go` in tests          |

### Acceptance Criteria (parsed)

A checklist of requirements using markdown checkbox syntax:

```markdown
## Acceptance Criteria
- [ ] Endpoint: `GET /api/v1/users/{id}/profile`
- [ ] Returns 200 with profile data
- [ ] Response includes: display_name, avatar_url
- [ ] Response excludes: email, internal_id
- [ ] Returns 404 if user not found
```

Each criterion is evaluated independently during verification. At runtime, any matching baseline invariants are added to this set automatically.

### Complete example

```markdown
# Add user profile endpoint

## Intent
Public endpoint for retrieving basic user profile information.
Returns display name and avatar, but not email or internal IDs.

## Scope
- **modify:** `src/handlers/profile.go`, `src/models/user.go`
- **call:** `user-service`
- **forbid:** `src/auth/*`, database migrations

## Acceptance Criteria
- [ ] Endpoint: `GET /api/v1/users/{id}/profile`
- [ ] Returns 200 with profile data
- [ ] Response includes: display_name, avatar_url
- [ ] Response excludes: email, internal_id
- [ ] Returns 404 if user not found

## Execution Steps

### Step 1: Define the response model
Create a model that only includes the allowed fields.

### Step 2: Implement the handler
Create the HTTP handler that fetches and returns profile data.
```
