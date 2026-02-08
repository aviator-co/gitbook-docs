# Setting up org invariants

Org invariants are rules that apply to every change in your organization. Instead of adding “requires authentication” to every spec, you define it once as an invariant and it’s checked automatically.

In this tutorial, you’ll create a security invariant that enforces authentication on HTTP handlers.

**Time:** 10 minutes

**Prerequisites:**

* Admin access to your Aviator organization
* Completed the Your first spec tutorial (recommended)

### What you’ll do

1. Create an org invariant for authentication
2. Test it against an existing change
3. See how invariants appear in verification results

### Step 1: Navigate to invariants

Go to **Verify → Settings → Org Invariants**.

You’ll see a list of existing invariants (possibly empty) and a button to create new ones.

### Step 2: Create a new invariant

Click **New Invariant**.

Give it a name: `security-baseline`

Add a description: `Core security requirements for all code changes`

### Step 3: Define the rules

In the invariant editor, add your security rules:

```markdown
# Security Baseline

## Rules

### Authentication
-All HTTP handlers must use AuthMiddleware
-No endpoint may bypass authentication without explicit exception

### Secrets
-No hardcoded credentials, API keys, or tokens
-Secrets must come from environment variables or secret manager
-No secrets in log output

### Input Validation
-All external input must be validated before use
-SQL queries must use parameterized statements
```

These rules are written in natural language. Verify understands them semantically.

### Step 4: Configure scope

Invariants can apply to all files or specific paths. For this security baseline:

**Applies to:** `All files` (default)

Some invariants might only apply to certain areas:

* API rules might apply only to `src/handlers/**`
* Database rules might apply only to `src/db/**`

For security, we want it everywhere. Leave it as “All files.”

### Step 5: Save and enable

Click **Save Invariant**.

The invariant is now active. Toggle the **Enabled** switch if it isn’t already on.

### Step 6: Test with a verification

Let’s see the invariant in action. Go back to one of your PRs that has verification results, or create a new spec and implement it.

When verification runs, you’ll see the invariant checks in the results:

```
✓ Org Invariants: PASSED (5/5)

  security-baseline:
    ✓ All HTTP handlers must use AuthMiddleware
    ✓ No hardcoded credentials, API keys, or tokens
    ✓ Secrets must come from environment variables
    ✓ No secrets in log output
    ✓ SQL queries must use parameterized statements
```

### Step 7: See a violation

Create a spec and implementation that violates an invariant. For example, add a hardcoded API key:

```go
const apiKey = "sk_live_abc123..."
```

When verification runs:

```
✗ Org Invariants: FAILED (4/5)

  security-baseline:
    ✓ All HTTP handlers must use AuthMiddleware
    ✗ No hardcoded credentials, API keys, or tokens
    ✓ Secrets must come from environment variables
    ✓ No secrets in log output
    ✓ SQL queries must use parameterized statements

  Failure: Possible hardcoded API key detected
  Location: src/client.go:12

  Suggested fix: Move this credential to an environment variable
```

The invariant caught the violation even though the spec didn’t mention credentials.

### Step 8: Add an exception path

Some code legitimately doesn’t need certain checks. For example, your health check endpoint from the previous tutorial shouldn’t require authentication.

Edit the `security-baseline` invariant and add an exceptions section:

```markdown
## Exceptions

### Authentication exceptions
-Health check endpoints (`/health`, `/ready`, `/live`)
-Public documentation endpoints
```

Save the invariant. Now the authentication rule won’t fail for health check handlers.

### How invariants work with specs

Invariants and spec criteria work together:

| Source             | What it checks                            |
| ------------------ | ----------------------------------------- |
| **Spec criteria**  | Requirements specific to this change      |
| **Org invariants** | Organization-wide rules that always apply |

You don’t need to repeat invariant rules in your specs. If authentication is an invariant, you don’t need to add “requires authentication” to every spec—it’s checked automatically.

But you can override invariants in a spec. If your spec explicitly says “does not require authentication” and has a valid reason in the intent, verification understands the exception.

### What you learned

* Org invariants define rules that apply to all changes
* Rules are written in natural language
* Invariants are checked alongside spec criteria
* Exceptions can be defined for legitimate edge cases
* You don’t need to repeat invariant rules in individual specs

### Next steps

* [How-to: Add more invariants](setting-up-org-invariants.md) - Common invariant patterns
* [Configuration options](reference/configuration-reference.md) - All invariant settings
* [Verification layers](concepts/verification-layers.md) - How invariants, domain contracts, and specs work together
