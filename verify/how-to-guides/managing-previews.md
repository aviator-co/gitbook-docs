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

* **Registry-sourced images.** Push a new image to the same tag. Aviator re-pulls and rebuilds it automatically within the hour — and only when the tag's digest actually changed. To pull a new build immediately, open the image under **Settings → Sandboxes** and click **Check for Update**.
* **Dockerfile-built images.** Aviator builds these from the Dockerfile you provided at registration. When the contents need to change, register the updated image under **Settings → Sandboxes**.

Two patterns work well for either source:

* **Track a moving tag.** Reference a stable name/tag (e.g. `api-preview`) and let each new push flow through. Simple, recommended for most teams.
* **Pin an exact build per release.** For changes that must be reproducible months later for compliance, reference an image digest (`…@sha256:…`) or a versioned tag (e.g. `api-preview-v1.42.0`) from `verify.yaml` on the long-lived branch. The exact image used at verification time stays fixed.

### Multi-preview repos

Most repos start with a single `default` preview. Add a second when:

* **You have multiple services in one repo** that scenarios need to hit independently. Declare each as its own preview with its own port.
* **Some scenarios need much higher fidelity** (real third-party sandboxes, big seed data) and others don't. Split into a fast `light` preview and a slow `heavy` one — most scenarios target `light`; the few that need fidelity opt into `heavy`.
* **You have a prod-mirror configuration** worth verifying against. Declare it as `mirror` with production-like secrets and seed.

Don't proliferate previews for small differences. Two previews that share most of their setup are easier to maintain as one preview with a setup-script switch.

### TTL and cleanup

Previews are torn down after their run completes. The "Open preview" link in the review document keeps the container alive for a short window so reviewers can poke at it; once that window expires, it's gone.

If a reviewer needs longer access — for a security review, a customer demo, a deep debugging session — extend the preview from the review document UI. Extensions are bounded (typically a few hours), audited, and cost the team explicit acknowledgment.

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
