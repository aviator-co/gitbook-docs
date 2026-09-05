# Preview YAML reference

This page documents the `preview` block of your Verify configuration, edited in the Aviator dashboard under **Verify → Settings → Verify** (with the repo selected). For the concept of what a preview is and how it fits in, see [Concepts: Previews](../concepts/previews.md).

### Shape

`preview` is a list nested under the top-level `verify` key. Each entry defines one preview.

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

The `verify:` wrapper is required — the schema rejects unknown top-level keys, so a bare `preview:` block fails validation.

If a single preview is declared with no `name`, it's treated as `default`. Always set names explicitly when you have more than one.

### Preview methods

Every entry has a `method` that decides who owns the environment:

| Method              | Who builds the environment                                                                    | Use it when                                                                                        |
| ------------------- | ---------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| `sandbox` (default) | Aviator boots a container from a registered preview image and runs your setup script.          | You want Aviator to own the environment end to end.                                                 |
| `endpoint`          | Your CI builds and deploys the environment, then registers its URL with Aviator.               | You already deploy a per-PR environment, or the environment needs infrastructure Aviator can't host. |

`method` is optional and defaults to `sandbox`, so a config written without it keeps behaving exactly as before. The sections below document the sandbox fields; for the endpoint schema, see [Endpoint previews](#endpoint-previews-method-endpoint).

When a repo declares more than one preview, the first entry whose method is usable governs the run.

### Sandbox fields

| Field      | Type            | Required | Description                                                                                              |
| ---------- | --------------- | -------- | -------------------------------------------------------------------------------------------------------- |
| `name`     | string          | no       | Unique name within the repo. Defaults to `default`. Scenarios target a preview by this name.            |
| `method`   | string          | no       | `sandbox` or `endpoint`. Defaults to `sandbox`.                                                          |
| `image`    | string          | yes      | Name of a preview image. Aviator caches the image locally and boots a container from it per run.        |
| `port`     | int             | yes      | Port the app serves on. Aviator handles exposing this as a public URL.                                   |
| `setup`    | string          | no       | Path (in the repo) to a setup script. Defaults to `.aviator/scripts/preview-setup.sh`. Runs after the container starts. |
| `teardown` | string          | no       | Optional path (in the repo) to a teardown script. Runs before the container is destroyed.                |
| `secrets`  | list of strings | no       | Account secret keys. Each is injected into the container as an environment variable of the same name.   |
| `verify_skill` | string | no | Repo-relative path to this preview's [Verify skill](../how-to-guides/writing-a-skill-md.md) entry point. Overrides the default `.aviator/verify/skills/<preview-name>.md` lookup. |

### Image

`image` is the name of a preview image registered with Aviator for your account. Aviator caches the image locally and boots a container from it for each verification run.

You can register an image through **Settings → Sandbox** in the Aviator UI. The same image system backs both the preview environment and the coding sandbox — set both to the same image when you want them to share dependencies and tooling.

### Secrets

`secrets` is a list of secret keys defined on your account. Each name resolves to a value at boot time and is injected into the container as an environment variable of the same name:

```yaml
secrets:
  - DB_PASSWORD       # → env DB_PASSWORD=<resolved value>
  - STRIPE_KEY        # → env STRIPE_KEY=<resolved value>
```

Secrets are managed in the Aviator UI under **Settings → Secrets**. Scoped per account, granted to repos explicitly. The preview container never sees the unresolved name — only the value.

### Verify skill

By default, Verify reads this preview's app-driving guidance from `.aviator/verify/skills/<preview-name>.md` (the entry-point file may reference other files in the repo). To point the preview at a file elsewhere, set a single path:

```yaml
verify_skill: docs/verify/main.md
```

The path is repo-relative; when set, it replaces the default `<preview-name>.md` lookup. See [Writing a Verify skill](../how-to-guides/writing-a-skill-md.md).

### Setup script

`setup` runs inside the container after start. Use it for things that aren't baked into the image:

```bash
#!/usr/bin/env bash
set -euo pipefail

# Migrations
./bin/api migrate

# Light fixtures
./bin/api seed --fixture=tests/fixtures/preview.json
```

Two rules:

* **Idempotent.** The script runs every time the preview boots. Don't assume a clean slate elsewhere.
* **Fast.** Heavy work (large fixtures, dependency installs) should be baked into the image. Setup-time work is part of every run's latency.

See [Managing previews](../how-to-guides/managing-previews.md) for the bake-vs-setup tradeoff.

### Teardown script

`teardown` is optional. When provided, it runs before the container is destroyed. Use it to release external resources the preview acquired (e.g. a per-run tenant in a shared test environment).

### Anatomy

<figure><img src="../../.gitbook/assets/verify-preview-anatomy.svg" alt="Preview anatomy: inputs, the preview container, and what consumes it"><figcaption><p>How the YAML fields translate to a running preview</p></figcaption></figure>

### Sandbox examples

**Minimal:**

```yaml
verify:
  preview:
    - name: default
      image: api-preview
      port: 8000
      setup: .aviator/scripts/preview-setup.sh
      secrets:
        - PREVIEW_DB_PASSWORD
```

**Multi-preview repo:**

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
    - name: worker
      image: worker-preview
      port: 9000
      secrets:
        - DB_PASSWORD
        - QUEUE_URL
```

### Endpoint previews (`method: endpoint`)

An endpoint preview is hosted outside Aviator. Your CI pipeline builds and deploys the environment for the pull request and registers its URL with Aviator; Aviator correlates that registration to the verification session, waits for it when a run needs it, drives it, and honors its expiry. It never builds or tears the environment down.

```yaml
verify:
  preview:
    - name: ci
      method: endpoint
      wait_timeout_sec: 1800     # how long a verification run waits for a registered preview
      expires_default_sec: 7200  # registration lifetime when a registration omits expires_at
```

| Field                 | Type   | Required | Description                                                                                              |
| --------------------- | ------ | -------- | ---------------------------------------------------------------------------------------------------------- |
| `name`                | string | no       | Unique name within the repo. Defaults to `default`. Scenarios target a preview by this name.              |
| `method`              | string | yes      | Must be `endpoint`.                                                                                        |
| `wait_timeout_sec`    | int    | no       | How long a verification run waits for a registered preview before falling back to code analysis. Defaults to 1800. Range 60–7200. |
| `expires_default_sec` | int    | no       | How long a registration stays current when it doesn't supply its own `expires_at`. Defaults to 7200. Range 300–86400. |
| `verify_skill`        | string | no       | Repo-relative path to this preview's [Verify skill](../how-to-guides/writing-a-skill-md.md) entry point. Overrides the default `.aviator/verify/skills/<preview-name>.md` lookup. |

Endpoint entries take none of the sandbox fields — `image`, `port`, `setup`, `teardown`, and `secrets` are all rejected, because the environment isn't Aviator's to build. Credentials for driving the app come from the registration itself.

Registering a preview is an API call from your CI pipeline. See [Registering an external preview](../how-to-guides/registering-an-external-preview.md) for the request shape, the waiting behavior, and the expiry and deregistration semantics.

### See also

* [Concepts: Previews](../concepts/previews.md)
* [Creating a preview](../how-to-guides/creating-a-preview.md)
* [Managing previews](../how-to-guides/managing-previews.md)
* [Registering an external preview](../how-to-guides/registering-an-external-preview.md)
