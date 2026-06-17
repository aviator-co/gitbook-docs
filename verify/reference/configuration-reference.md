---
description: Configuration surface for Verify — per-repo verify.yaml and org-level settings.
---

# Configuration reference

This page is the umbrella reference for Verify configuration. Per-concept references live on their own pages and are linked from here.

### Per-repo: `aviator/verify.yaml`

The root of all per-repo configuration. Lives at `aviator/verify.yaml` in your repo's default branch.

#### Top-level keys

| Key                      | Type        | Default | Description                                                                  |
| ------------------------ | ----------- | ------- | ---------------------------------------------------------------------------- |
| `require_verification`   | boolean     | `false` | If true, every PR to a protected branch must have a verification run.        |
| `exempt_paths`           | string list | `[]`    | Glob patterns. PRs touching only exempt paths skip verification.             |
| `block_on_failure`       | boolean     | `true`  | Failed verification blocks merge via the GitHub check.                       |
| `draft_pr_verification`  | boolean     | `false` | Run verification on draft PRs.                                               |
| `preview`                | list        | none    | One or more preview definitions. See [Preview YAML](preview-yaml.md).        |

Example:

```yaml
require_verification: true
exempt_paths:
  - "docs/**"
  - "*.md"
  - ".github/**"
block_on_failure: true
draft_pr_verification: false

preview:
  - name: default
    image: ghcr.io/acme/api:edge
    image_pull_secret: GHCR_PULL_TOKEN
    port: 8000
    setup: .aviator/scripts/preview-setup.sh
    secrets:
      - DB_PASSWORD
      - STRIPE_KEY
```

Dashboard-level settings override the file. If both define `block_on_failure`, the dashboard value wins.

#### Exempt path glob syntax

| Pattern      | Matches                                  |
| ------------ | ---------------------------------------- |
| `docs/**`    | Any file under the `docs/` directory     |
| `*.md`       | Markdown files in the repo root          |
| `**/*.md`    | Markdown files anywhere in the repo      |
| `.github/**` | Files under `.github/`                   |

A PR that modifies *only* exempt-path files skips verification entirely. A PR that modifies any non-exempt file runs the full pipeline.

### Per-repo: preview block

Detailed in its own reference page:

→ [Preview YAML reference](preview-yaml.md)

### Org-level settings

Configure in **Verify → Settings**.

#### Notifications

| Setting             | Type    | Default | Description                                                |
| ------------------- | ------- | ------- | ---------------------------------------------------------- |
| `slack_channel`     | string  | none    | Default Slack channel for verification failure notifications. |
| `email_on_failure`  | boolean | `true`  | Email the submission author when verification fails.        |
| `post_pr_comment`   | boolean | `true`  | Post failure details as a PR comment on GitHub.             |

#### Timeouts

| Setting                  | Type    | Default | Description                                                  |
| ------------------------ | ------- | ------- | ------------------------------------------------------------ |
| `verification_timeout`   | integer | `300`   | Maximum seconds for a verification run before it's failed.   |
| `preview_idle_ttl`       | integer | `1800`  | Seconds the preview stays alive after the run for reviewer access. |

If verification times out, the run is marked failed with a `timeout` reason. Re-running is safe — the previous run's state doesn't carry forward.

### Invariants

Invariants have their own configuration surface in **Settings → Invariants**. Field-level reference and writing guidance are on the concept and tutorial pages:

* [Concepts: Invariants](../concepts/invariants.md) — scope precedence, categories, anti-patterns
* [Setting up org invariants](../setting-up-org-invariants.md) — step-by-step setup

### MCP

The MCP install and tool surface are on a dedicated reference page:

→ [MCP tools](mcp-tools.md)

### API

#### Authentication

```bash
export AVIATOR_API_KEY="av_live_..."
```

Generate keys in **Settings → API Keys**. API keys are distinct from MCP tokens — keys authenticate programmatic access; MCP tokens authenticate agent submissions.

#### Base URL

```
https://api.aviator.co/v1/verify
```

#### Rate limits

| Tier       | Requests/hour |
| ---------- | ------------- |
| Free       | 100           |
| Team       | 1,000         |
| Enterprise | 10,000        |

### Environment variables

For CI/CD integration:

| Variable                  | Description                                              |
| ------------------------- | -------------------------------------------------------- |
| `AVIATOR_API_KEY`         | API key for programmatic access.                         |
| `AVIATOR_ORG`             | Organization slug. Used by CLI and CI integrations.      |
| `AVIATOR_VERIFY_ENABLED`  | Set to `false` to disable Verify in a specific CI job.   |

### See also

* [Preview YAML reference](preview-yaml.md)
* [MCP tools](mcp-tools.md)
* [Spec format](spec-format.md)
* [Concepts: Invariants](../concepts/invariants.md)
