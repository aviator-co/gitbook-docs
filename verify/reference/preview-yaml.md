# Preview YAML reference

This page documents the `preview` block in `aviator/verify.yaml`. For the concept of what a preview is and how it fits in, see [Concepts: Previews](../concepts/previews.md).

### Shape

`preview` is a list. Each entry defines one preview.

```yaml
preview:
  - name: default
    image: registry.io/api:edge
    port: 8000
    setup: .aviator/scripts/preview-setup.sh
    secrets:
      - DB_PASSWORD
      - STRIPE_KEY
```

The first preview is implicitly `default` if no name is given — but always declare names explicitly when you have more than one.

### Fields

| Field                | Type            | Required | Status        | Description                                                                                              |
| -------------------- | --------------- | -------- | ------------- | -------------------------------------------------------------------------------------------------------- |
| `name`               | string          | yes      | GA            | Unique name within the repo. Scenarios target a preview by this name.                                    |
| `image`              | string          | yes      | GA            | Image source. Currently supports an image uploaded directly to Aviator. Registry URL support — see below.|
| `port`               | int             | yes      | GA            | Port the preview exposes. Used by the scenario runner and the "Open preview" link.                       |
| `setup`              | string          | no       | GA            | Path (in the repo) to a setup script that runs after the container starts. Used for migrations, seeding. |
| `secrets`            | list of strings | no       | GA            | Secret-store entry names. Each is injected into the container as an environment variable of the same name.|
| `image_pull_secret`  | string          | no       | In development| Secret-store entry holding registry credentials, used to pull private images.                            |
| `skills_dir`         | string          | no       | In development| Path (in the repo) to a directory of SKILL files. See [Writing a SKILL.md](../how-to-guides/writing-a-skill-md.md).|
| `env`                | map             | no       | In development| Inline environment variables (not from the secret store). Use sparingly — secrets belong in `secrets`.   |
| `health_check`       | object          | no       | In development| Override the default port-ready check. See "Health check" below.                                         |

Fields marked **In development** are usable today but the field name or shape may change. They're documented here so you can opt in early; we'll call out breaking changes in release notes.

### Image source

`image` accepts two forms:

* **Aviator-uploaded image** — a short name referencing an image uploaded directly to Aviator. This is what most existing previews use today.
* **Registry URL** — a full image reference like `registry.io/api:edge`. If the registry is private, set `image_pull_secret` to the name of a secret-store entry that holds the registry credentials.

```yaml
preview:
  - name: default
    image: ghcr.io/acme/api:edge
    image_pull_secret: GHCR_PULL_TOKEN
    port: 8000
```

### Secrets

`secrets` is a list of secret-store entry names. Each name resolves to a value at boot time and is injected into the container as an environment variable of the same name:

```yaml
secrets:
  - DB_PASSWORD       # → env DB_PASSWORD=<resolved value>
  - STRIPE_KEY        # → env STRIPE_KEY=<resolved value>
```

Secrets are managed in the Aviator UI under **Settings → Secrets**. They're scoped per org and granted to repos explicitly. The preview container never sees the unresolved name — only the value.

Don't put secrets in `env`. Use `env` only for non-sensitive overrides (feature flags, region selectors).

### Setup script

`setup` runs inside the container after start, before the port is considered ready. Use it for things that aren't baked into the image:

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

### Health check

By default, the preview is considered ready when the declared port accepts a TCP connection. Override with `health_check` for HTTP-level readiness:

```yaml
health_check:
  path: /healthz
  expect_status: 200
  timeout: 30s
```

### Anatomy

<figure><img src="../../.gitbook/assets/verify-preview-anatomy.svg" alt="Preview anatomy: inputs, the preview container, and what consumes it"><figcaption><p>How the YAML fields translate to a running preview</p></figcaption></figure>

### Examples

**Minimal — Aviator-uploaded image:**

```yaml
preview:
  - name: default
    image: sim-preview-baked-deps
    port: 8000
    setup: .aviator/scripts/preview-setup.sh
    secrets:
      - PREVIEW_DB_PASSWORD
```

**Private registry with multi-preview:**

```yaml
preview:
  - name: default
    image: ghcr.io/acme/api:edge
    image_pull_secret: GHCR_PULL_TOKEN
    port: 8000
    setup: .aviator/scripts/preview-setup.sh
    skills_dir: .aviator/skills/api
    secrets:
      - DB_PASSWORD
      - STRIPE_KEY
  - name: worker
    image: ghcr.io/acme/worker:edge
    image_pull_secret: GHCR_PULL_TOKEN
    port: 9000
    skills_dir: .aviator/skills/worker
    secrets:
      - DB_PASSWORD
      - QUEUE_URL
```

### See also

* [Concepts: Previews](../concepts/previews.md)
* [Creating a preview](../how-to-guides/creating-a-preview.md)
* [Managing previews](../how-to-guides/managing-previews.md)
