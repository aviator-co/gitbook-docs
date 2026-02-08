# Spec format

Complete reference for Verify spec syntax.

### Structure

Every spec has a title and three required sections:

```markdown
# Title

## Intent
[description]

## Scope
[scope declarations]

## Acceptance Criteria
[list of criteria]

## Execution steps
[steps to execute]
```

### Title

Short description of the change. Appears in dashboard, PR checks, and audit trail.

```markdown
# Add user profile endpoint
```

### Intent

Plain-language description of what and why. Provides context for verification.

```markdown
## Intent
Public endpoint for retrieving basic user profile information.
Returns display name and avatar, but not email or internal IDs.
```

### Scope

Declares what the change may touch using three keywords.

#### Keywords

| Keyword  | Purpose                     | Required |
| -------- | --------------------------- | -------- |
| `modify` | Files to create or edit     | Yes      |
| `call`   | External services to access | No       |
| `forbid` | Prohibited files or actions | No       |

#### Syntax

```markdown
## Scope
-**modify:** `src/handlers/profile.go`, `src/models/user.go`
-**call:** `user-service`
-**forbid:** `src/auth/*`, database migrations
```

#### Glob patterns

| Pattern                | Matches                                      |
| ---------------------- | -------------------------------------------- |
| `src/handlers/*.go`    | .go files in src/handlers                    |
| `src/handlers/**/*.go` | .go files in src/handlers and subdirectories |
| `tests/*_test.go`      | Files ending in \_test.go in tests           |

### Acceptance Criteria

Checklist of requirements using markdown checkbox syntax.

```markdown
## Acceptance Criteria
-[ ] Endpoint: `GET /api/v1/users/{id}/profile`
-[ ] Returns 200 with profile data
-[ ] Response includes: display_name, avatar_url
-[ ] Response excludes: email, internal_id
-[ ] Returns 404 if user not found
```

Each criterion is checked independently during verification.

### Complete example

```markdown
# Add user profile endpoint

## Intent
Public endpoint for retrieving basic user profile information.
Returns display name and avatar, but not email or internal IDs.

## Scope
-**modify:** `src/handlers/profile.go`, `src/models/user.go`
-**call:** `user-service`
-**forbid:** `src/auth/*`, database migrations

## Acceptance Criteria
-[ ] Endpoint: `GET /api/v1/users/{id}/profile`
-[ ] Returns 200 with profile data
-[ ] Response includes: display_name, avatar_url
-[ ] Response excludes: email, internal_id
-[ ] Returns 404 if user not found

## Execution Steps

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

### Validation errors

| Error                     | Fix                                       |
| ------------------------- | ----------------------------------------- |
| Missing required section  | Add Intent, Scope, or Acceptance Criteria |
| Empty acceptance criteria | Add at least one criterion                |
| Invalid scope keyword     | Use `modify`, `call`, or `forbid`         |
| Invalid glob pattern      | Check pattern syntax                      |
