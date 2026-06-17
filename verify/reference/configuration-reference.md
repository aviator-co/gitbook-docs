---
description: Configuration surface for Verify — per-repo verify.yaml and org-level settings.
---

# Configuration reference

This page is the umbrella reference for Verify configuration. Per-concept references live on their own pages and are linked from here.

### Per-repo: `aviator/verify.yaml`

The root of all per-repo configuration. Lives at `aviator/verify.yaml` in your repo's default branch.

#### Top-level keys

| Key             | Type        | Default | Description                                                                  |
| --------------- | ----------- | ------- | ---------------------------------------------------------------------------- |
| `exempt_paths`  | string list | `[]`    | Glob patterns. PRs touching only exempt paths skip verification.             |
| `preview`       | list        | none    | One or more preview definitions. See [Preview YAML](preview-yaml.md).        |

Example:

```yaml
exempt_paths:
  - "docs/**"
  - "*.md"
  - ".github/**"

preview:
  - name: default
    image: sim-preview-baked-deps
    port: 8000
    setup: .aviator/scripts/preview-setup.sh
    secrets:
      - DB_PASSWORD
      - STRIPE_KEY
```

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

#### Preview lifetime

| Setting            | Type    | Default | Description                                                          |
| ------------------ | ------- | ------- | -------------------------------------------------------------------- |
| `preview_idle_ttl` | integer | `1800`  | Seconds the preview stays alive after the run for reviewer access.   |

When the TTL expires, the preview is torn down. Reviewers can extend a preview manually from the review document — see [Managing previews](../how-to-guides/managing-previews.md).

### Invariants

Invariants have their own configuration surface in **Settings → Invariants**. Field-level reference and writing guidance are on the concept and tutorial pages:

* [Concepts: Invariants](../concepts/invariants.md) — sources, categories, selection
* [Setting up org invariants](../setting-up-org-invariants.md) — step-by-step setup

### MCP

The MCP install and tool surface are on a dedicated reference page:

→ [MCP tools](mcp-tools.md)

### See also

* [Preview YAML reference](preview-yaml.md)
* [MCP tools](mcp-tools.md)
* [Spec format](spec-format.md)
* [Concepts: Invariants](../concepts/invariants.md)
