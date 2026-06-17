# Creating a preview

This guide walks through adding a preview to a repo end-to-end: uploading an image to Aviator, injecting runtime secrets, declaring the preview in `aviator/verify.yaml`, and confirming it boots.

By the end, you'll have a working `default` preview that scenarios can run against.

**Time:** ~15 minutes

**Prerequisites:**

* Admin access to your Aviator org (for managing secrets and images)
* A container image of your service
* The repo connected to Aviator — see [Connect a repository](connect-a-repository.md)

For the concept overview, see [Concepts: Previews](../concepts/previews.md). For the full field list, see [Preview YAML reference](../reference/preview-yaml.md).

### Step 1: Upload the image to Aviator

Today, previews boot from images uploaded directly to Aviator (registry-URL support is planned — see [Preview YAML reference — Image source](../reference/preview-yaml.md#image-source)).

1. Open **Settings → Images** in the Aviator UI.
2. Upload your image. Give it a short, stable name — e.g. `api-preview`. You'll reference this name from `verify.yaml`.
3. Grant the image to the repo that will use it.

If you build images on every merge, automate this step from your CI so the image stays fresh without manual reupload.

### Step 2: Store runtime secrets

Anything your service reads from the environment at runtime — DB password, third-party API keys — needs to be a secret.

For each runtime secret:

1. Open **Settings → Secrets**.
2. Add the secret using the *exact name* your service expects in `process.env` (or its language equivalent). Example: `DB_PASSWORD`, not `PG_PASSWORD`.
3. Grant it to the repo.

Naming matters: each secret is injected into the preview as an environment variable of the same name.

### Step 3: Declare the preview in verify.yaml

Add a `preview` block to `aviator/verify.yaml` at the root of your repo:

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

* **`image`** — the name you used when uploading. Aviator resolves this to the latest version you've uploaded under that name.
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

Commit the `verify.yaml` change and the setup script. Open a PR.

When the next Verify run starts, watch the **Run** tab in the review document:

* You'll see a "Building preview" line while the image is pulled.
* Then "Booting preview" while the setup script runs.
* Then "Preview ready" once the port accepts connections.
* Scenarios start executing.

If the preview fails to boot, the failure shows up here with the exit code and the last 50 lines of container output.

### Step 6: Verify the open-preview link

Once a run produces a verdict, open the review document. The "Active run" panel includes an **Open preview** link. Click it — it should land you at the preview's port, accessible from your browser.

If the link works, the preview is fully wired. Reviewers can now open it for ad-hoc exploration during review.

### Common boot failures

| Symptom                                         | Likely cause                                                                          |
| ----------------------------------------------- | ------------------------------------------------------------------------------------- |
| `image not found`                               | The image name in `verify.yaml` doesn't match what's uploaded, or the image isn't granted to this repo. |
| `MissingSecret: DB_PASSWORD`                    | The secret isn't created or isn't granted to this repo.                               |
| Container exits 0 immediately                   | The container's CMD ran a one-off command and exited. Add a long-running process.     |
| Port never opens                                | Your service binds to `localhost` only. Bind to `0.0.0.0` inside the container.       |
| Setup script exits non-zero                     | Migration failure or fixture path wrong. The container output shows the exact line.   |

### Next steps

* [Managing previews](managing-previews.md) — bake-vs-setup, refresh, multi-preview repos
* [Seed data for previews](seed-data-for-previews.md) — fixture strategies
* [Writing a SKILL.md](writing-a-skill-md.md) — give the scenario runner context for your preview
