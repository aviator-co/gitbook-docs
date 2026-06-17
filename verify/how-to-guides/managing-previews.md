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
| You control the image build and can rebuild on CI                              | The image is a vendor artifact you can't modify                              |

Default: bake heavy work into the image. Use `setup` for the things that genuinely have to be fresh.

A preview that takes 90 seconds to boot is a preview people stop trusting. If you're consistently over 60 seconds, look at what's in `setup` and move what you can into the image.

### Keeping the image fresh

`image` references a tag, and Verify pulls that tag every run. The rollout point is wherever your CI pushes — there's nothing for Verify to refresh manually.

Two patterns work well:

* **Tag per branch or per merge.** CI pushes `registry.io/api:edge` on every merge to `main`. Verify always pulls the latest version of `main`. Simple, fast, recommended for most teams.
* **Tag per release.** CI pushes `registry.io/api:v1.42.0` on release. The preview pins to a specific release tag. Use this if you need verification runs to reproduce months later for compliance — the image used at verification time stays available.

Avoid `:latest`. It's ambiguous about *which* latest, breaks under registry caching, and makes audit trails harder to reconstruct.

### Multi-preview repos

Most repos start with a single `default` preview. Add a second when:

* **You have multiple services in one repo** that scenarios need to hit independently. Declare each as its own preview with its own port.
* **Some scenarios need much higher fidelity** (real third-party sandboxes, big seed data) and others don't. Split into a fast `light` preview and a slow `heavy` one — most scenarios target `light`; the few that need fidelity opt into `heavy`.
* **You have a prod-mirror configuration** worth verifying against. Declare it as `mirror` with production-like secrets and seed.

Don't proliferate previews for small differences. Two previews that share most of their setup are easier to maintain as one preview with a setup-script switch.

### TTL and cleanup

Previews are torn down after their run completes. The "Open preview" link in the review document keeps the container alive for a short window so reviewers can poke at it; once that window expires, it's gone.

If a reviewer needs longer access — for a security review, a customer demo, a deep debugging session — extend the preview from the review document UI. Extensions are bounded (typically a few hours), audited, and cost the team explicit acknowledgment.

If you find people extending the same preview repeatedly, that's a signal — either the preview is hard to reproduce locally (consider seeding more state) or the review pattern is wrong (longer-lived staging environments live outside Verify).

### When the preview is wrong

Sometimes the preview itself is the problem, not the code being verified. Signs:

* **Scenarios fail randomly across runs** — usually preview state isn't deterministic. See [Seed data for previews](seed-data-for-previews.md).
* **Scenarios fail consistently in an unrelated area** — usually a missing secret or migration that should run in `setup`.
* **The first scenario passes, the rest fail** — usually the preview lacks a reset hook and the first run mutated state the others depend on.
* **The reviewer's open-preview link 404s or hangs** — usually the container has crashed in the background. Check the run's container output for the exit reason.

When a preview misbehaves, fix the preview before you debug the code. A flaky preview produces flaky verdicts and erodes trust in verification fast.

### Updating an existing preview

Most preview changes are safe to ship as a normal commit — verify.yaml is read fresh every run.

Two exceptions:

* **Removing a secret your service still reads.** The preview will boot, but the service will fail at first use. Update the service first, then trim the secret.
* **Changing `port`.** Update the service's port binding in the same commit. A port mismatch makes the preview never become ready, which looks like a generic boot failure.

### See also

* [Creating a preview](creating-a-preview.md)
* [Seed data for previews](seed-data-for-previews.md)
* [Preview YAML reference](../reference/preview-yaml.md)
* [Concepts: Previews](../concepts/previews.md)
