# Your first spec

In this tutorial, you'll create a spec for a small change, implement code against it, and see verification run. By the end, you'll understand the complete Verify workflow.

**Time:** ~15 minutes

**Prerequisites:**

* An Aviator account with Verify enabled
* A connected GitHub repository (see [Connect a repository](how-to-guides/connect-a-repository.md))
* Write access to the repository

### What you'll do

1. Create a runbook with a spec
2. Push a branch and open a PR
3. Watch verification run against the criteria
4. See pass and fail cases

### Step 1: Create a runbook

Go to **Verify → Runbooks** in your Aviator dashboard and click **New Runbook**.

Select your repository from the dropdown if you have multiple connected.

The runbook is where the spec, the verification, and the criteria conversation all live for this change.

### Step 2: Describe what you want to build

In the chat panel, describe your change in plain language:

```
Add a health check endpoint at /health that returns {"status": "ok"}
with a 200 response. It should not require authentication.
```

The AI assistant analyzes your description and proposes a structured spec.

### Step 3: Review the proposed spec

You should see something like this:

```markdown
# Add health check endpoint

## Intent
Public health check endpoint for load balancer and monitoring
integration. Returns service status without requiring authentication.

## Scope
- **modify:** `src/handlers/health.go`, `src/router.go`
- **forbid:** authentication logic, database queries

## Acceptance Criteria
- [ ] Endpoint: `GET /health`
- [ ] Returns HTTP 200
- [ ] Response body: `{"status": "ok"}`
- [ ] Does not require authentication
- [ ] Response time under 50ms

## Execution Steps
...
```

Review each section:

* **Intent** — Does this capture what you're trying to do?
* **Scope** — Are the file paths right for your codebase?
* **Acceptance Criteria** — Are these the right requirements?

Only **Scope** and **Acceptance Criteria** are parsed by Verify. Intent and Execution Steps are useful for human reviewers and for AI implementation tools, but Verify doesn't check them as separate sections.

### Step 4: Adjust the spec

If anything looks wrong, ask the assistant to update it. For example:

```
The routes file is src/router.go, not src/routes.go
```

The spec updates. You can also edit the markdown directly.

You can keep iterating on the criteria at any point — including after code has been written. Verification re-runs against the current set of criteria each time.

### Step 5: Implement the code

When the spec looks right, implement the change. You can:

* Paste the spec into an AI coding tool (Cursor, Copilot, Claude, etc.)
* Implement it manually
* Use any combination

Here's a simple Go implementation:

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

Commit and push:

```bash
git checkout -b add-health-endpoint
git add .
git commit -m "Add health check endpoint"
git push origin add-health-endpoint
```

### Step 6: Open a PR and link it

Open a PR for your branch on GitHub.

If `auto_create_runbook_on_pr_open` is enabled on your repository, the runbook may already be linked. Otherwise, link the runbook to the PR from the runbook's page in the dashboard.

### Step 7: Watch verification run

After the PR is linked, verification starts automatically.

You'll see a GitHub check:

```
◐ aviator/verify — Running...
```

Verification typically completes in 30–90 seconds.

### Step 8: Check the results

If everything is correct, you'll see:

```
✓ aviator/verify — All criteria passed
  5/5 criteria passed
```

Click **Details** to see the full report:

```
✓ Endpoint: GET /health
✓ Returns HTTP 200
✓ Response body: {"status": "ok"}
✓ Does not require authentication
✓ Response time under 50ms
```

The dashboard's runbook page has the full per-criterion detail with reasons and evidence.

### What if verification fails?

If you accidentally left in an auth check, you might see:

```
✗ aviator/verify — 1 criterion failed
  4/5 criteria passed

✗ Does not require authentication
  Handler is wrapped by AuthMiddleware in src/router.go
```

To fix it:

1. Remove the auth middleware from this route
2. Push the fix
3. Verification re-runs automatically

If the verifier disagrees with you about whether a criterion is satisfied, you can also sharpen the criterion text in the runbook chat. Verification re-runs against the updated criteria.

### What you learned

* A runbook holds the spec, the conversation, and the verification for a change
* The parser uses `## Scope` and `## Acceptance Criteria` sections
* Criteria can be edited at any time
* Verification checks each criterion and reports pass/fail with evidence
* The result appears as a GitHub PR check

### Next steps

* [Setting up baseline invariants](setting-up-org-invariants.md) — Add repo-wide rules
* [Writing effective acceptance criteria](how-to-guides/writing-effective-acceptance-criteria.md) — Improve your specs
* [How verification works](concepts/how-verification-works.md) — Understand what happens during verification
