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
| `verify.filters` | map  | none    | Which pull requests get an auto-created review. See [Filters](#per-repo-filters). |

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

### Per-repo: filters

Filters narrow which pull requests get an [auto-created review](github-integration.md#how-a-pr-gets-its-review), for example to keep Verify off docs-only changes or PRs from a team you don't verify. They only matter when **Auto-create review on PR open** is on, and never apply to a PR that's already linked to a review, such as one submitted with `/verify-submit`.

| Key              | Type | Description                                                         |
| ---------------- | ---- | ------------------------------------------------------------------- |
| `authors`        | list | Only auto-create for PRs from these authors.                        |
| `authors_ignore` | list | Never auto-create for PRs from these authors.                       |
| `paths`          | list | Only auto-create when the PR changes at least one matching file.    |
| `paths_ignore`   | list | Never auto-create when every file the PR changes matches.           |

A PR has to pass every filter that's set. When it doesn't, Verify posts a skipped `aviator/verify` check that names the filter it matched.

**Authors** are GitHub logins or teams written as `@org/team`. Logins match case-insensitively, and `*` matches any characters, so `*[bot]` matches every GitHub App bot. A team matches its members, including members of nested teams. Saving fails if a team doesn't exist.

**Paths** are gitignore-style globs. A `!` prefix excludes a path, and later patterns win over earlier ones. Use either `paths` or `paths_ignore`, not both.

This example auto-creates reviews for PRs that change something under `src/` other than Markdown, unless someone on `@acme/release-eng` opened them:

```yaml
verify:
  filters:
    authors_ignore:
      - "@acme/release-eng"
    paths:
      - "src/**"
      - "!src/**/*.md"
```

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

Invariants have their own configuration surface in **Verify → Invariants**. Field-level reference and writing guidance are on the concept and tutorial pages:

* [Concepts: Invariants](../concepts/invariants.md) — sources, categories, selection
* [Setting up org invariants](../setting-up-org-invariants.md) — step-by-step setup

### See also

* [Preview YAML reference](preview-yaml.md)
* [Concepts: Invariants](../concepts/invariants.md)
