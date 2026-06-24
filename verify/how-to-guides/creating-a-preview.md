# Creating a preview

This guide walks through adding a preview to a repo end-to-end: registering an image with Aviator, declaring runtime secrets, configuring the preview block in `aviator/verify.yaml`, and confirming it boots.

By the end, you'll have a working `default` preview that scenarios can run against.

> Previews are optional. Verify works without one — every criterion routes to code-scan in that case. Add a preview when behavioral verdicts (endpoint contracts, UI flows, error shapes) start mattering for your team. See [Concepts: Previews](../concepts/previews.md).

**Time:** ~15 minutes

**Prerequisites:**

* Admin access to your Aviator account (to manage images and secrets)
* The repo connected to Aviator — see [Connect a repository](connect-a-repository.md)

For the concept overview, see [Concepts: Previews](../concepts/previews.md). For the full field list, see [Preview YAML reference](../reference/preview-yaml.md).

### Step 1: Register a preview image

Aviator boots a preview container from an image you register under **Settings → Sandboxes**. Give it a short, stable name — e.g. `api-preview` — which you'll reference from `verify.yaml`. There are two ways to register one.

#### Option A: From a Dockerfile

Provide a Dockerfile and Aviator builds the image for you. Choose this when you want Aviator to own the build. `git`, `git-lfs`, and the coding-agent CLI are installed automatically; `COPY` and `ADD` instructions aren't supported.

#### Option B: From a container registry image

Point Aviator at an image you already publish to your own registry — AWS ECR, Google Artifact/Container Registry, or any generic registry (Docker Hub, GHCR, Artifactory, self-hosted). Choose this when you already build and push the image in your own CI.

1. Open **Settings → Sandboxes → Add → From Registry Image**.
2. Select the registry type and enter the fully-qualified image reference, e.g. `123456789.dkr.ecr.us-west-2.amazonaws.com/api:latest`.
3. Provide **read-only** pull credentials. Scope them as tightly as possible — Aviator only needs to pull and inspect the image:
   * **AWS ECR** — an access key ID, secret access key, and region for an IAM principal with `AmazonEC2ContainerRegistryReadOnly` on the repository.
   * **GCP** — a service-account key (JSON) with `roles/artifactregistry.reader` on the repository.
   * **Generic** — a username and password/token (omit both for a public image).

   Credentials are stored encrypted and are never displayed again.

The image **must include `git`** — Aviator checks out the branch under test into the container at boot.

When you push a new image to the same tag, Aviator picks it up automatically the next time a preview starts: it re-pulls and rebuilds only when the tag's digest has actually changed. To pin an exact build instead, reference an image digest (`…@sha256:…`) rather than a tag.

If you don't have a service image yet, the simplest path is to start with a base image that has your runtime (e.g. `node:20`) and let the setup script install dependencies. As your previews mature, bake more into the image.

### Step 2: Declare runtime secrets

Anything your service reads from the environment at runtime — DB password, third-party API keys — needs to be a secret.

For each runtime secret:

1. Open **Settings → Secrets**.
2. Add the secret using the *exact name* your service expects (e.g. `DB_PASSWORD`, not `PG_PASSWORD`).
3. Grant it to the repo.

Naming matters: each secret is injected into the preview as an environment variable of the same name.

### Step 3: Declare the preview in verify.yaml

Add a `preview` block to `aviator/verify.yaml`:

```yaml
preview:
  - name: default
    image: api-preview
    port: 8000
    setup: .aviator/scripts/preview-setup.sh
    secrets:
      - DB_PASSWORD
      - STRIPE_KEY
```

Three things to get right:

* **`image`** — the name you registered in Step 1.
* **`port`** — the port your service listens on inside the container. This is what the scenario runner connects to.
* **`secrets`** — every name listed here must exist in the secret store and be granted to this repo, or the boot will fail with a clear error.

If you don't need a setup script (the image is fully self-contained), omit the `setup` line.

### Step 4: Write the setup script (optional)

`setup` runs inside the container after start and before the port is marked ready. It's the right place for migrations, light fixtures, and warm-up.

Create `.aviator/scripts/preview-setup.sh` in the repo:

```bash
#!/usr/bin/env bash
set -euo pipefail

# Run migrations on the test database.
./bin/api migrate

# Seed a small fixture so scenarios have something to query.
./bin/api seed --fixture=tests/fixtures/preview.json
```

Make it executable:

```bash
chmod +x .aviator/scripts/preview-setup.sh
```

Two rules of thumb:

* **Make it idempotent.** It runs every time the preview boots.
* **Keep it fast.** Heavy work belongs in the image, not in setup. See [Managing previews](managing-previews.md) for the bake-vs-setup tradeoff.

### Step 5: Commit and trigger a verification

Commit the `verify.yaml` change and the setup script. Submit through the MCP (or open a PR if you've already submitted).

When the next verification run starts, watch the run timeline in the review document:

* You'll see a "Building preview" line while the container is prepared.
* Then "Booting preview" while the setup script runs.
* Then "Preview ready" once the port accepts connections.
* Scenarios start executing.

If the preview fails to boot, the failure shows up here with the exit code and the last lines of container output.

### Step 6: Verify the open-preview link

Once a run produces a verdict, open the review document. The run panel includes an **Open preview** link. Click it — it should land you at the preview's port, accessible from your browser.

If the link works, the preview is fully wired. Reviewers can now open it for ad-hoc exploration during review.

### Common boot failures

| Symptom                                         | Likely cause                                                                          |
| ----------------------------------------------- | ------------------------------------------------------------------------------------- |
| `image not found`                               | The image name in `verify.yaml` doesn't match what's registered in **Sandboxes**, or the image isn't granted to this repo. |
| Registry pull / auth error                      | For a registry-sourced image, the stored pull credentials are wrong, expired, or lack read access to the repository. Re-enter them in **Sandboxes**. |
| Missing secret error                            | A name in `secrets` isn't defined or isn't granted to this repo.                       |
| Container exits 0 immediately                   | The container's CMD ran a one-off command and exited. Add a long-running process.     |
| Port never opens                                | Your service binds to `localhost` only. Bind to `0.0.0.0` inside the container.       |
| Setup script exits non-zero                     | Migration failure or fixture path wrong. The container output shows the exact line.   |

### Next steps

* [Managing previews](managing-previews.md) — bake-vs-setup, refresh, multi-preview repos
* [Seed data for previews](seed-data-for-previews.md) — fixture strategies
* [Writing a SKILL.md](writing-a-skill-md.md) — give the scenario runner context for your preview
