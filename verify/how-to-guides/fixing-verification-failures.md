# Fixing verification failures

When verification fails, you need to either fix your code or update your spec. This guide covers common failures and how to resolve them.

### Understanding the failure

First, read the failure details carefully. Click **Details** on the GitHub check, or open the runbook's verify page in the dashboard, to see:

* Which criterion failed
* The verifier's reason
* The file and line number (where the verifier could identify them)
* A short evidence snippet

### Criteria failures

#### Criterion not satisfied

```
✗ Response excludes: internal_id, billing_provider_id
   Found: Response includes billing_provider_id
   Location: src/models/subscription.go:24
```

**Cause:** Your implementation doesn't satisfy a requirement.

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

Push the fix. Verification re-runs automatically.

#### Criterion ambiguous (verifier disagrees with you)

If you believe your code satisfies the criterion but the verifier disagrees:

1. **Check the criterion wording.** Is it specific enough? "Requires authentication" might be interpreted differently than "Handler calls AuthMiddleware."
2. **Edit the criterion.** Acceptance criteria can be updated in the runbook chat. Re-run verification after editing.
3. **Add context to the spec's Intent.** Help the verifier understand how your implementation works.
4. **Make the criterion more specific.** Replace vague criteria with concrete ones.

Because the verifier is LLM-driven, edge-case judgements can vary. Sharpening the criterion text usually resolves repeated false positives.

### Invariant failures

When a baseline invariant fails, the GitHub check shows the same kind of result as any other criterion — the invariant's rule text is included in the criteria set for the run.

```
✗ No hardcoded credentials, API keys, or tokens
   Found: Possible API key at src/client.go:23
```

**Cause:** Your code violates a repo-wide rule.

**Fix:** Address the issue:

```go
// Before
const apiKey = "sk_live_abc123..."

// After
var apiKey = os.Getenv("API_KEY")
```

#### Legitimate exception

If your code legitimately doesn't follow an invariant:

1. Document the exception in the spec's Intent so the verifier sees the rationale
2. If the exception is recurring, edit the invariant's conditions so it doesn't apply to this case
3. If the verifier still flags it, sharpen the invariant rule text

### Re-running verification

After fixing issues, verification re-runs automatically when you push a new commit.

You can also trigger it manually from the runbook's verify page in the dashboard.

### Editing the spec vs. fixing the code

| Situation                              | Action                                      |
| -------------------------------------- | ------------------------------------------- |
| Code doesn't do what the spec says     | Fix the code                                |
| Spec is wrong about requirements       | Edit the criteria in the runbook chat       |
| Verifier is wrong about a verdict      | Make the criterion more specific            |

Criteria can be edited at any time — there is no formal "approved and locked" state today.

### Getting help

If you can't resolve a failure:

1. Ask on Discord: [discord.gg/aviator](https://discord.gg/MmQWrY9xrA)
2. Email support: [support@aviator.co](mailto:support@aviator.co)

Include the verification run ID when asking for help.

### See also

* [Reference: Verification results](../reference/understanding-verification-results.md)
* [How to write effective criteria](writing-effective-acceptance-criteria.md)
