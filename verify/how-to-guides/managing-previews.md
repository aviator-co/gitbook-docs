# Managing previews

Once a preview is up, the ongoing work is keeping it fast, fresh, and matched to what scenarios need. This guide covers the recurring decisions.

For setup, see [Creating a preview](creating-a-preview.md). For the schema, see [Preview YAML reference](../reference/preview-yaml.md).

### Bake into the image or run at setup time?

Every preview has work that has to happen before scenarios run: dependencies, migrations, fixtures, warm-up. You can put that work in the image build, or in the `setup` script that runs at boot.

| Put it in the image when…                                                       | Put it in `setup` when…                                                       |
| ------------------------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| It's slow (`npm install`, downloading large blobs, compiling assets)            | It depends on per-run state (current DB schema, fresh fixtures)              |
| It rarely changes (system packages, language runtime, compiled binaries)       | It changes every run (re-seeding, applying migrations from `HEAD`)            |
| It doesn't depend on secrets that are only available at runtime                | It needs runtime secrets to complete                                         |

Default: bake heavy work into the image. Use `setup` for the things that genuinely have to be fresh.

A preview that takes 90 seconds to boot is a preview people stop trusting. If you're consistently over 60 seconds, look at what's in `setup` and move what you can into the image.

### Keeping the image fresh

`image` references a preview image registered with Aviator. Aviator caches the built image and boots from the cached copy per run, so a preview is only as fresh as the last build it picked up. How it refreshes depends on how the image is registered:

* **Registry-sourced images.** Push a new image to the same tag. Aviator re-pulls and rebuilds it automatically within the hour — and only when the tag's digest actually changed. To pull a new build immediately, open the image under **Settings → Sandbox** and click **Check for Update**.
* **Dockerfile-built images.** Aviator builds these from the Dockerfile you provided at registration. When the contents need to change, register the updated image under **Settings → Sandbox**.

Two patterns work well for registry-sourced images. Both are set on the image itself under **Settings → Sandbox**, not in `verify.yaml` — the `image` field there only names the registered image:

* **Track a moving tag.** Register the image against a stable tag (e.g. `registry.example.com/api-preview:latest`) and let each new push flow through. Simple, recommended for most teams.
* **Pin an exact build per release.** For changes that must be reproducible months later for compliance, register the image by digest (`…@sha256:…`) or by a versioned tag (e.g. `registry.example.com/api-preview:v1.42.0`). The exact image used at verification time stays fixed.

### Multi-preview repos

Most repos start with a single `default` preview. Add a second when:

* **You have multiple services in one repo** that scenarios need to hit independently. Declare each as its own preview with its own port.
* **Some scenarios need much higher fidelity** (real third-party sandboxes, big seed data) and others don't. Split into a fast `light` preview and a slow `heavy` one — most scenarios target `light`; the few that need fidelity opt into `heavy`.
* **You have a prod-mirror configuration** worth verifying against. Declare it as `mirror` with production-like secrets and seed.

Don't proliferate previews for small differences. Two previews that share most of their setup are easier to maintain as one preview with a setup-script switch.

### TTL and cleanup

Previews are torn down when their sandbox times out. The "Open preview" link in the review document stays live until then, so reviewers can poke at the container; once it expires, it's gone.

The window is the account-wide **Sandbox Timeout (minutes)** setting under **Verify → Settings → Sandbox** — 60 minutes by default, adjustable from 1 to 240. If reviewers need longer access for a security review, a customer demo, or a deep debugging session, raise that setting, or launch the preview again from the review document when they're ready to look.

### Updating an existing preview

Most preview changes are low-risk — your setup script is read fresh from the branch every run, and config changes take effect as soon as you save them in Verify settings.

Two exceptions:

* **Removing a secret your service still reads.** The preview will boot, but the service will fail at first use. Update the service first, then trim the secret.
* **Changing `port`.** Update the service's port binding in the same commit. A port mismatch makes the preview never become ready, which looks like a generic boot failure.

### See also

* [Creating a preview](creating-a-preview.md)
* [Seed data for previews](seed-data-for-previews.md)
* [Preview YAML reference](../reference/preview-yaml.md)
* [Concepts: Previews](../concepts/previews.md)
