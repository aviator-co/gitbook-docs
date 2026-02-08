---
description: All configuration options for Verify.
---

# Configuration reference

### Repository settings

Configure in **Verify → Repositories → \[repo] → Settings** or via `.aviator/verify.yaml`.

| Setting                 | Type      | Default | Description                         |
| ----------------------- | --------- | ------- | ----------------------------------- |
| `require_specs`         | boolean   | `false` | PRs must have a linked spec         |
| `exempt_paths`          | string\[] | `[]`    | File patterns that don’t need specs |
| `auto_link_specs`       | boolean   | `true`  | Auto-link specs based on branch/PR  |
| `block_on_failure`      | boolean   | `true`  | Failed verification blocks merge    |
| `draft_pr_verification` | boolean   | `false` | Run verification on draft PRs       |

#### Configuration file

Create `.aviator/verify.yaml` in your repository’s default branch:

```yaml
require_specs:true
exempt_paths:
-"docs/**"
-"*.md"
-".github/**"
-"**/*.test.ts"
auto_link_specs:true
block_on_failure:true
draft_pr_verification:false
```

Dashboard settings override the config file.

#### Exempt paths

Patterns use glob syntax:

| Pattern      | Matches                    |
| ------------ | -------------------------- |
| `docs/**`    | Anything in docs directory |
| `*.md`       | Markdown files in root     |
| `**/*.md`    | Markdown files anywhere    |
| `.github/**` | GitHub config files        |

PRs that only modify exempt paths don’t require specs.

### Organization settings

Configure in **Verify → Settings**.

#### General

| Setting                 | Type    | Default | Description                         |
| ----------------------- | ------- | ------- | ----------------------------------- |
| `default_require_specs` | boolean | `false` | Default for new repositories        |
| `allow_self_approval`   | boolean | `false` | Authors can approve their own specs |
| `approval_count`        | integer | `1`     | Required approvals per spec         |

#### Notifications

| Setting            | Type    | Default | Description                             |
| ------------------ | ------- | ------- | --------------------------------------- |
| `slack_channel`    | string  | none    | Default Slack channel for notifications |
| `email_on_failure` | boolean | `true`  | Email authors on verification failure   |
| `post_pr_comment`  | boolean | `true`  | Post failure details as PR comment      |

#### Timeouts

| Setting                 | Type    | Default | Description                           |
| ----------------------- | ------- | ------- | ------------------------------------- |
| `verification_timeout`  | integer | `300`   | Max seconds for verification          |
| `spec_approval_timeout` | integer | `0`     | Hours before spec expires (0 = never) |

### Org invariants

Configure in **Verify → Settings → Org Invariants**.

#### Invariant structure

```yaml
name: security-baseline
description: Core security requirements
enabled:true
applies_to:"**/*"  # or specific paths
rules:|
  # Security Baseline

  ## Rules
  - All HTTP handlers must use AuthMiddleware
  - No hardcoded credentials
  - SQL queries must use parameterized statements

  ## Exceptions
  - Health check endpoints (/health, /ready)
```

#### Invariant options

| Field         | Type    | Required | Description                       |
| ------------- | ------- | -------- | --------------------------------- |
| `name`        | string  | Yes      | Unique identifier                 |
| `description` | string  | No       | Human-readable description        |
| `enabled`     | boolean | Yes      | Whether invariant is active       |
| `applies_to`  | string  | No       | Glob pattern (default: all files) |
| `rules`       | string  | Yes      | Markdown-formatted rules          |

### Spec settings

#### Spec lifecycle

| State             | Editable | Can verify |
| ----------------- | -------- | ---------- |
| Draft             | Yes      | No         |
| In Review         | No       | No         |
| Changes Requested | Yes      | No         |
| Approved          | No       | Yes        |

#### Spec expiration

If `spec_approval_timeout` is set, approved specs expire after that many hours without verification. Expired specs must be re-approved.

### API configuration

#### Authentication

```bash
export AVIATOR_API_KEY="av_live_..."
```

Generate keys in **Settings → API Keys**.

#### Base URL

```
<https://api.aviator.co/v1/verify>
```

#### Rate limits

| Tier       | Requests/hour |
| ---------- | ------------- |
| Free       | 100           |
| Team       | 1000          |
| Enterprise | 10000         |

### Environment variables

For CI/CD integration:

| Variable                 | Description                              |
| ------------------------ | ---------------------------------------- |
| `AVIATOR_API_KEY`        | API authentication                       |
| `AVIATOR_ORG`            | Organization slug                        |
| `AVIATOR_VERIFY_ENABLED` | Enable/disable verification (true/false) |

### See also

* [How to connect a repository](../how-to-guides/connect-a-repository.md)
* [Tutorial: Setting up org invariants](configuration-reference.md#org-invariants)
