# Fixing verification failures

When verification fails, you need to either fix your code or update your spec. This guide covers common failures and how to resolve them.

### Understanding the failure

First, read the failure details carefully. Click **Details** on the GitHub check to see:

* Which check failed (scope, criteria, or invariant)
* The specific reason
* The file and line number
* A suggested fix

### Scope violations

#### File not in declared scope

```
❌ Scope: FAILED
   Modified file not in declared scope: src/auth/middleware.go
```

**Cause:** You changed a file that isn’t listed in your spec’s `modify` section.

**Options:**

1.  **Remove the changes** if they shouldn’t be part of this change:

    ```bash
    git checkout origin/main -- src/auth/middleware.go
    git commit --amend
    git push -f
    ```
2. **Update the spec** if the file change is intentional:
   * Create a new spec revision that includes this file
   * Get it approved
   * Verification will re-run

#### Forbidden file modified

```
❌ Scope: FAILED
   Modified file matches forbidden pattern: src/auth/*
```

**Cause:** You changed a file explicitly forbidden in the spec.

**Options:**

1. Remove the changes to the forbidden file
2. Reconsider whether this file really needs to change—if so, create a new spec

### Criteria failures

#### Criterion not satisfied

```
❌ Response excludes: internal_id, billing_provider_id
   Found: Response includes billing_provider_id
   Location: src/models/subscription.go:24
```

**Cause:** Your implementation doesn’t satisfy a requirement.

**Fix:** Change your code to satisfy the criterion. In this case, remove the field from the response:

```go
// Before
type SubscriptionStatus struct {
    Status    string `json:"status"`
    BillingID string `json:"billing_provider_id"` // Remove this
    PlanName  string `json:"plan_name"`
}

// After
type SubscriptionStatus struct {
    Status   string `json:"status"`
    PlanName string `json:"plan_name"`
}
```

Push the fix. Verification runs automatically.

#### Criterion ambiguous (false positive)

If you believe your code satisfies the criterion but verification disagrees:

1. **Check the criterion wording.** Is it specific enough? “Requires authentication” might be interpreted differently than “Handler calls AuthMiddleware.”
2. **Add context to the Intent.** Help verification understand how your implementation works.
3. **Make the criterion more specific.** Replace vague criteria with concrete ones.
4. **Report the issue.** Click the feedback button in the verification report if you believe it’s a bug.

### Invariant violations

#### Security invariant failed

```
❌ Org Invariants: FAILED
   security-baseline: No hardcoded credentials
   Found: Possible API key at src/client.go:23
```

**Cause:** Your code violates an organization-wide rule.

**Fix:** Address the security issue:

```go
// Before
const apiKey = "sk_live_abc123..."

// After
var apiKey = os.Getenv("API_KEY")
```

#### Legitimate exception

If your code legitimately doesn’t need to follow an invariant:

1. Check if there’s an exception path configured for your case
2. Talk to whoever manages org invariants about adding an exception
3. Document in your spec’s Intent why this exception is valid

### Re-running verification

After fixing issues, verification re-runs automatically when you push.

You can also trigger it manually:

* Comment `/aviator verify` on the PR
* Click **Re-run** in the verification details page

### When to update the spec vs. fix the code

| Situation                              | Action                                      |
| -------------------------------------- | ------------------------------------------- |
| Code doesn’t do what spec says         | Fix the code                                |
| Spec is wrong about requirements       | Create new spec revision, get approval      |
| Forgot to include a file in scope      | Create new spec revision, get approval      |
| Verification is wrong (false positive) | Make criterion more specific, or report bug |

Remember: approved specs are locked. You can’t edit them—you create a new revision.

### Getting help

If you can’t resolve a failure:

1. Check common issues
2. Ask on Discord: [discord.gg/aviator](https://discord.gg/MmQWrY9xrA)
3. Email support: [support@aviator.co](mailto:support@aviator.co)

Include the verification ID when asking for help.

### See also

* Reference: Verification results
* Reference: Troubleshooting
* How to write effective criteria
