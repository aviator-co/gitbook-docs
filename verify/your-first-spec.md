# Your first spec

In this tutorial, you’ll create a spec, get it approved, implement code against it, and see verification in action. By the end, you’ll understand the complete Verify workflow.

**Time:** 15 minutes

**Prerequisites:**

* An Aviator account with a connected GitHub repository
* Write access to the repository

### What you’ll do

1. Create a spec for a simple code change
2. Submit it for review and approve it
3. Implement the change
4. See verification pass (or fail)

### Step 1: Open the spec editor

Go to **Verify → Specs** in your Aviator dashboard and click **New Spec**.

You’ll see a split view:

* Left: The spec editor
* Right: AI assistant chat

Select your repository from the dropdown if you have multiple connected.

### Step 2: Describe what you want to build

In the chat panel, describe your change in plain language:

```
Add a health check endpoint at /health that returns {"status": "ok"}
with a 200 response. It should not require authentication.
```

Click **Generate Spec**. The AI analyzes your description and generates a structured spec.

### Step 3: Review the generated spec

You should see something like this in the editor:

```markdown
# Add health check endpoint

## Intent
Public health check endpoint for load balancer and monitoring
integration. Returns service status without requiring authentication.

## Scope
-**modify:** `src/handlers/health.go`, `src/routes.go`
-**forbid:** authentication logic, database queries

## Acceptance Criteria
-[ ] Endpoint: `GET /health`
-[ ] Returns HTTP 200
-[ ] Response body: `{"status": "ok"}`
-[ ] Does not require authentication
-[ ] Response time under 50ms

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

Review each section:

**Intent** — Does this capture what you’re trying to do?

**Scope** — Are the files correct for your codebase? You might need to adjust the paths.

**Acceptance Criteria** — Are these the right requirements?

### Step 4: Adjust the spec

Let’s say your routes file is actually named `router.go`. Tell the assistant:

```
The routes file is src/router.go, not src/routes.go
```

The spec updates. You can also edit the markdown directly in the left panel.

### Step 5: Submit for review

When the spec looks right, click **Submit for Review**.

Select yourself as the reviewer (for this tutorial). In a real workflow, you’d select a teammate.

Add an optional note: “Tutorial spec - please approve”

Click **Submit**.

### Step 6: Approve the spec

Go to **Verify → Reviews**. You’ll see your pending spec.

Click to open it. As a reviewer, you’re checking:

* Does the intent make sense?
* Is the scope appropriate?
* Are the acceptance criteria complete and verifiable?

For this tutorial, everything looks good. Click **Approve**.

The spec status changes to **Approved**. It’s now locked and ready for implementation.

### Step 7: View the approved spec

Go back to **Verify → Specs** and open your spec. You’ll see:

* Status: Approved
* Who approved it and when
* **Copy Spec** and **Download Spec** buttons

Click **Copy Spec** to copy the markdown to your clipboard.

### Step 8: Implement the code

Now implement the endpoint. You can:

* Paste the spec into an AI coding tool (Cursor, Copilot, etc.)
* Implement it manually

Here’s a simple Go implementation:

```go
// src/handlers/health.go
package handlers

import (
    "encoding/json"
    "net/http"
)

func HealthCheck(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "application/json")
    w.WriteHeader(http.StatusOK)
    json.NewEncoder(w).Encode(map[string]string{"status": "ok"})
}
```

```go
// In src/router.go, add:
router.HandleFunc("/health", handlers.HealthCheck).Methods("GET")
```

Commit your changes and push to a branch:

```bash
git checkout -b add-health-endpoint
git add .
git commit -m "Add health check endpoint"
git push origin add-health-endpoint
```

### Step 9: Create a pull request

Open a PR for your branch on GitHub.

If you haven’t linked the spec to this PR, comment on the PR:

```
/aviator link <spec-id>
```

(You can find the spec ID in the URL when viewing the spec)

### Step 10: Watch verification run

After linking (or if auto-link is configured), verification starts automatically.

You’ll see a GitHub check:

```
◐ Aviator Verify — Running...
```

Wait for it to complete (usually under a minute for small changes).

### Step 11: Check the results

If everything is correct, you’ll see:

```
✓ Aviator Verify — All checks passed
  5/5 criteria passed
```

Click **Details** to see the full report:

```
✓ Scope: PASSED
  Modified files match declared scope

✓ Acceptance Criteria: PASSED (5/5)
  ✓ Endpoint: GET /health
  ✓ Returns HTTP 200
  ✓ Response body: {"status": "ok"}
  ✓ Does not require authentication
  ✓ Response time under 50ms

Audit ID: aud_abc123
```

Your PR is now clear to merge.

### What if verification fails?

Let’s say you accidentally included an auth check. Verification might show:

```
✗ Acceptance Criteria: FAILED (4/5)
  ✓ Endpoint: GET /health
  ✓ Returns HTTP 200
  ✓ Response body: {"status": "ok"}
  ✗ Does not require authentication
  ✓ Response time under 50ms

  Failure: Handler calls AuthMiddleware
  Location: src/router.go:15
```

To fix it:

1. Remove the auth middleware from this route
2. Push the fix
3. Verification runs again automatically

### What you learned

* Specs have three sections: Intent, Scope, and Acceptance Criteria
* The AI assistant helps generate and refine specs
* Specs must be approved before implementation
* Verification checks code against the approved spec
* Results show exactly what passed or failed

### Next steps

* [Setting up org invariants](setting-up-org-invariants.md) - Add organization-wide rules
* [How-to write effective acceptance criteria](how-to-guides/writing-effective-acceptance-criteria.md) - Improve your specs
* [How verification works](concepts/how-verification-works.md) - Understand what happens during verification
