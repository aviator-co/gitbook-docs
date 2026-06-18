# Creating a preview

This guide walks through adding a preview to a repo end-to-end: registering an image with Aviator, declaring runtime secrets, configuring the preview in your Aviator Verify settings, and confirming it boots.

By the end, you'll have a working `default` preview that scenarios can run against.

> Previews are optional. Verify works without one — every criterion routes to code-scan in that case. Add a preview when behavioral verdicts (endpoint contracts, UI flows, error shapes) start mattering for your team. See [Concepts: Previews](../concepts/previews.md).

**Time:** ~15 minutes

**Prerequisites:**

* Admin access to your Aviator account (to manage images and secrets)
* The repo connected to Aviator — see [Connect a repository](connect-a-repository.md)

For the concept overview, see [Concepts: Previews](../concepts/previews.md). For the full field list, see [Preview YAML reference](../reference/preview-yaml.md).

### Step 1: Register a preview image

Aviator boots a preview container from an image you register under **Settings → Sandboxes**. Give it a short, stable name — e.g. `api-preview` — which you'll reference from your preview config. There are two ways to register one.

#### Option A: From a Dockerfile

Provide a Dockerfile and Aviator builds the image for you. Choose this when your service's environment can be assembled from packages and you want Aviator to own the build.

Bake in everything your service needs to run — the language runtime, system packages, and dependencies (`apt` / `npm` / `pip` installs). `git`, `git-lfs`, and the coding-agent CLI are added automatically. `COPY` and `ADD` **aren't supported**, so you can't bake your own source into the image this way — Aviator checks out the branch under test at boot, and your setup script (Step 4) builds and starts the service from that checkout.

#### Option B: From a container registry image

Point Aviator at an image you already publish to your own registry — AWS ECR, Google Artifact/Container Registry, or any generic registry (Docker Hub, GHCR, Artifactory, self-hosted).

Choose this when you want the preview to run your *real* environment rather than one reconstructed from a Dockerfile:

* **Bake in anything, not just packages.** Since an Aviator-built Dockerfile can't `COPY`/`ADD`, it can only install public packages. A registry image can contain whatever your build produces — compiled binaries, private artifacts, a fully prepared environment.
* **Reuse the image your CI already builds.** One build definition instead of a second Dockerfile to keep in sync, so previews mirror what you actually ship — and it covers private base images and build-time secrets Aviator's builder can't use.
* **Faster, prod-like boots.** Bake the repository into the image (see **Repo root** below) and Aviator fetches the branch in place instead of cloning it fresh each run.

1. Open **Settings → Sandboxes → Add → From Registry Image**.
2. Select the registry type and enter the fully-qualified image reference, e.g. `123456789.dkr.ecr.us-west-2.amazonaws.com/api:latest`.
3. Provide **read-only** pull credentials. Scope them as tightly as possible — Aviator only needs to pull and inspect the image:
   * **AWS ECR** — an access key ID, secret access key, and region for an IAM principal with `AmazonEC2ContainerRegistryReadOnly` on the repository.
   * **GCP** — a service-account key (JSON) with `roles/artifactregistry.reader` on the repository.
   * **Generic** — a username and password/token (omit both for a public image).

   Credentials are stored encrypted and are never displayed again.
4. *(Optional)* If your image already has the repository checked out — for example, baked in during your own CI build — set **Repo root** to that path (e.g. `/code`). The preview runs from there instead of cloning the branch fresh. Leave it blank to have Aviator clone the branch at boot.

The image **must include `git`** — Aviator checks out (or updates) the branch under test inside the container at boot.

After you register the image, Aviator pulls and prepares it. The image card shows **Building**, then **Success** once it's ready — reference it from your preview config at that point.

**Keeping a registry image current.** When you push a new image to the same tag, Aviator re-pulls and rebuilds it automatically within the hour — and only when the tag's digest has actually changed, so an unchanged tag costs nothing. To pull a new build immediately, open the image under **Settings → Sandboxes** and click **Check for Update**. To pin an exact build that never moves, reference an image digest (`…@sha256:…`) instead of a tag.

If you don't have a service image yet, the simplest path is to start with a base image that has your runtime (e.g. `node:20`) and let the setup script install dependencies. As your previews mature, bake more into the image.

### Step 2: Declare runtime secrets

Anything your service reads from the environment at runtime — DB password, third-party API keys — needs to be a secret.

For each runtime secret:

1. Open **Settings → Secrets**.
2. Add the secret using the *exact name* your service expects (e.g. `DB_PASSWORD`, not `PG_PASSWORD`).
3. Grant it to the repo.

Naming matters: each secret is injected into the preview as an environment variable of the same name.

### Step 3: Configure the preview

Previews are configured in your **Aviator Verify settings**. Add a `preview` entry under `verify`:

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

The fields to get right:

* **`image`** — the image name you registered in Step 1.
* **`port`** — the port your service listens on inside the container. This is what the scenario runner connects to.
* **`setup`** — path in your repo to the script that starts your service (see Step 4).
* **`secrets`** — every name listed here must exist in your secret store and be granted to this repo, or the boot fails with a clear error.

### Step 4: Write the setup script

The setup script is what **starts your service** — it's the launch point of every preview, so this step isn't optional. Aviator runs it once inside the container at boot, after checking out the branch under test, and it's also where migrations, fixtures, and warm-up belong. Your declared secrets and a `PREVIEW_URL` variable are injected into the script's environment.

Create `.aviator/scripts/preview-setup.sh` in the repo (at the path your `setup` field points to):

```bash
#!/usr/bin/env bash
set -euo pipefail

# Build/install from the checked-out source, run migrations, seed fixtures.
./bin/api migrate
./bin/api seed --fixture=tests/fixtures/preview.json

# Start the service in the BACKGROUND so this script can exit — Aviator marks
# the preview ready once the script returns. Listen on your configured port.
nohup ./bin/api serve --port 8000 > /tmp/preview.log 2>&1 &

# There's no health check, so give the port a moment to come up before exiting.
sleep 2
```

Make it executable:

```bash
chmod +x .aviator/scripts/preview-setup.sh
```

Two rules of thumb:

* **Start the service in the background and let the script return.** Aviator marks the preview ready when the script exits `0` — there's no separate health check — so background your server and confirm the port is listening before the script finishes. If the script runs the server in the foreground, it never returns and the boot times out.
* **Keep it fast.** Heavy, rarely-changing work (dependency installs, compilation) belongs in the image, not here — setup time is part of every run's latency. See [Managing previews](managing-previews.md) for the bake-vs-setup tradeoff.

### Step 5: Commit and trigger a verification

Save your preview config in Verify settings and commit the setup script to the repo. Submit through the MCP (or open a PR if you've already submitted).

When the next verification run starts, watch the run timeline in the review document:

* You'll see a "Building preview" line while the container is prepared.
* Then "Booting preview" while the setup script runs.
* Then "Preview ready" once the setup script finishes.
* Scenarios start executing.

If the preview fails to boot, the failure shows up here with the exit code and the last lines of container output.

### Step 6: Verify the open-preview link

Once a run produces a verdict, open the review document. The run panel includes an **Open preview** link. Click it — it should land you at the preview's port, accessible from your browser.

If the link works, the preview is fully wired. Reviewers can now open it for ad-hoc exploration during review.

### Common boot failures

| Symptom                                         | Likely cause                                                                          |
| ----------------------------------------------- | ------------------------------------------------------------------------------------- |
| `image not found`                               | The image name in your preview config doesn't match what's registered in **Sandboxes**, or the image isn't granted to this repo. |
| Registry pull / auth error                      | For a registry-sourced image, the stored pull credentials are wrong, expired, or lack read access to the repository. Re-enter them in **Sandboxes**. |
| Missing secret error                            | A name in `secrets` isn't defined or isn't granted to this repo.                       |
| Setup script never finishes / times out         | The script runs the server in the foreground and blocks. Start it in the background so the script can exit. |
| Scenarios can't reach the service               | It bound to `localhost` only, or exited before listening. Bind to `0.0.0.0` on the configured port and confirm it's up before the setup script returns. |
| Setup script exits non-zero                     | Migration failure or fixture path wrong. The container output shows the exact line.   |

### Next steps

* [Managing previews](managing-previews.md) — bake-vs-setup, refresh, multi-preview repos
* [Seed data for previews](seed-data-for-previews.md) — fixture strategies
* [Writing a SKILL.md](writing-a-skill-md.md) — give the scenario runner context for your preview
