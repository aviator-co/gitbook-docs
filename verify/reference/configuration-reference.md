---
description: Configuration surface for Verify — per-repo verify.yaml and account-level settings.
---

# Configuration reference

This page is the umbrella reference for Verify configuration. Per-concept references live on their own pages and are linked from here.

### Per-repo: `verify.yaml`

The root of all per-repo configuration. Edit it in the Aviator dashboard under **Verify → Settings → Verify** with the repository selected — it is not a file you commit to the repo. Each save is versioned, and earlier versions can be restored from the editor's history.

#### Top-level keys

| Key              | Type | Default | Description                                                           |
| ---------------- | ---- | ------- | --------------------------------------------------------------------- |
| `verify`         | map  | none    | Root key. All Verify configuration nests under it.                    |
| `verify.preview` | list | none    | One or more preview definitions. See [Preview YAML](preview-yaml.md). |

Example:

```yaml
verify:
  preview:
    - name: default
      image: api-preview
      port: 8000
      setup: .aviator/scripts/preview-setup.sh
      secrets:
        - DB_PASSWORD
        - STRIPE_KEY
```

Unknown keys are rejected: saving fails with a validation error rather than silently ignoring them. A common mistake is starting the document at `preview:` — it must nest under `verify:`.

### Per-repo: preview block

Detailed in its own reference page:

→ [Preview YAML reference](preview-yaml.md)

### Account-level settings

Configure in **Verify → Settings**. These apply across the account rather than per repository.

#### Sandbox

Set under **Verify → Settings → Sandbox**.

| Setting                       | Type    | Default | Description                                                                 |
| ----------------------------- | ------- | ------- | --------------------------------------------------------------------------- |
| **Sandbox Timeout (minutes)** | integer | `60`    | How long a sandbox stays active. Accepts 1–240.                             |
| **Sandbox Image**             | template | Aviator default | The image sandboxes boot from. Custom images are registered on the same page. |

When the timeout expires, the sandbox is torn down and the preview link stops resolving. See [Managing previews](../how-to-guides/managing-previews.md).

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
