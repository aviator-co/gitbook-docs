# GitHub integration

Technical reference for Verify’s GitHub integration.

### GitHub App

Verify uses the Aviator GitHub App for repository access.

#### Permissions

| Permission           | Access     | Purpose                             |
| -------------------- | ---------- | ----------------------------------- |
| Repository contents  | Read       | Analyze code during verification    |
| Pull requests        | Read/Write | Post verification results, comments |
| Checks               | Read/Write | Create PR status checks             |
| Organization members | Read       | Suggest reviewers                   |
| Metadata             | Read       | Basic repository info               |

#### Installation

The app can be installed with:

* **All repositories** — Access to every repo in the org
* **Selected repositories** — Access only to chosen repos

Change access in GitHub: **Organization Settings → Installed GitHub Apps → Aviator → Configure**

### PR checks

#### Check name

```
Aviator Verify
```

#### Check statuses

| Status          | GitHub state      | Description                           |
| --------------- | ----------------- | ------------------------------------- |
| Pending         | `queued`          | Verification not started              |
| Running         | `in_progress`     | Verification running                  |
| Passed          | `success`         | All checks passed                     |
| Failed          | `failure`         | One or more checks failed             |
| Error           | `failure`         | Verification error (not code failure) |
| Action required | `action_required` | Needs attention (e.g., no spec)       |

#### Check output

Title format:

```
All checks passed
```

or

```
2 checks failed
```

Summary format:

```
7/7 criteria passed · 12 org invariants passed
```

### Slash commands

Commands are triggered by PR comments.

| Command                   | Action                   |
| ------------------------- | ------------------------ |
| `/aviator verify`         | Trigger verification     |
| `/aviator link <spec-id>` | Link spec to PR          |
| `/aviator status`         | Show verification status |

Commands are case-insensitive. The bot responds with a confirmation comment.

### PR comments

#### Failure comment

When verification fails, Aviator posts a summary comment:

```markdown
## Aviator Verify — Failed

❌ 2 checks failed

### Failures

**Response excludes: internal_id, billing_provider_id**
Found billing_provider_id in response struct.
📍 src/models/subscription.go:24

**Returns 404 if subscription not found**
Handler returns 500 instead of 404 when subscription is nil.
📍 src/handlers/subscription.go:47

[View full report →](<https://app.aviator.co/verify/>...)
```

Configure comments in **Verify → Settings → Notifications**.

#### Comment options

| Option          | Description                      | Default |
| --------------- | -------------------------------- | ------- |
| Post on failure | Comment when verification fails  | On      |
| Post on pass    | Comment when verification passes | Off     |
| Update existing | Update previous comment vs new   | On      |

### Webhooks

Aviator sends webhooks for verification events.

#### Events

| Event                    | Trigger               |
| ------------------------ | --------------------- |
| `verification.started`   | Verification begins   |
| `verification.completed` | Verification finishes |
| `spec.approved`          | Spec is approved      |

#### Payload

```json
{
  "event": "verification.completed",
  "verification_id": "ver_abc123",
  "status": "passed",
  "repository": "your-org/your-repo",
  "pr_number": 234,
  "timestamp": "2024-01-28T16:01:23Z"
}
```

Configure webhooks in **Verify → Settings → Webhooks**.

### Branch protection

To require Verify:

1. Repository Settings → Branches → Add rule
2. Enable “Require status checks to pass”
3. Search and select “Aviator Verify”

#### Recommended settings

```
☑ Require status checks to pass before merging
  ☑ Aviator Verify
☑ Require branches to be up to date before merging (optional)
☐ Require conversation resolution before merging
```

### Auto-linking

Verify can automatically link specs to PRs.

#### Methods

| Method         | How it works                     |
| -------------- | -------------------------------- |
| Branch name    | Spec ID or title in branch name  |
| PR description | Spec ID mentioned in description |
| Manual link    | `/aviator link` command          |

#### Branch name patterns

```
spec/add-subscription-endpoint
feature/spec_abc123-add-endpoint
```

#### PR description pattern

```markdown
Spec: spec_abc123
```

or

```markdown
Spec: verify/spec_abc123
```

### Rate limits

| Operation          | Limit                      |
| ------------------ | -------------------------- |
| Verification runs  | 100/hour per repository    |
| API requests       | 1000/hour per organization |
| Webhook deliveries | No limit                   |

### See also

* [How to configure branch protection](../how-to-guides/configuring-branch-protection.md)
* [How to connect a repository](../how-to-guides/connect-a-repository.md)
