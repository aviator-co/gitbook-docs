# Previews

A **preview** is an ephemeral environment Verify builds on demand and runs scenarios against. It's how Verify makes behavioral claims real — every "the endpoint returns X" criterion needs the code to actually run somewhere, and the preview is that somewhere.

A preview is short-lived: built per run, used by the scenario runner (and optionally a human reviewer), then torn down. No state persists between runs.

### Lifecycle

<figure><img src="../../.gitbook/assets/verify-preview-lifecycle.svg" alt="Preview lifecycle: define, build, boot, use, teardown"><figcaption><p>A preview moves through five phases per verification run</p></figcaption></figure>

| Phase         | What happens                                                                       |
| ------------- | ---------------------------------------------------------------------------------- |
| **Define**    | The repo declares the preview in `aviator/verify.yaml`.                            |
| **Build**     | Aviator pulls the image, injects secrets, and stages files from the repo.          |
| **Boot**     | The container starts. The setup script runs. The declared port becomes reachable.  |
| **Use**       | Scenarios execute against the preview. Reviewers can also open it from the UI.     |
| **Teardown**  | The container is destroyed. No state survives.                                     |

Every verification run starts fresh. That's deliberate — a preview that carries state from a previous run produces non-reproducible verdicts, which defeats the point of verification.

### Composition

A preview is composed of inputs from three places: an image uploaded to Aviator, your secret store, and your repo.

<figure><img src="../../.gitbook/assets/verify-preview-anatomy.svg" alt="Preview anatomy: inputs, the preview container, and what consumes it"><figcaption><p>Where each piece of a preview comes from, and what uses it</p></figcaption></figure>

* **Image** — an image you've uploaded to Aviator. Pulling from your own container registry is planned; see [Preview YAML reference — Image source](../reference/preview-yaml.md#image-source).
* **Secrets** — runtime secrets (DB passwords, API keys) are referenced by name from the secret store and injected as environment variables when the container boots.
* **Setup script** — optional. Runs after the container starts and before the port is marked ready. Used for migrations, seeding, warm-up.
* **Skills directory** — optional. Tells the scenario runner about the preview — base URL, test users, fixture references. See [Writing a SKILL.md](../how-to-guides/writing-a-skill-md.md).
* **Port** — the port the runner connects to. The container is considered ready when this port accepts connections (plus an optional health check).

Aviator stitches these together into a single ephemeral container. The repo's `verify.yaml` is the contract — see [Preview YAML reference](../reference/preview-yaml.md) for every field.

### Multiple previews per repo

A repo can declare more than one preview. Common patterns:

| Pattern                        | Why                                                                                   |
| ------------------------------ | ------------------------------------------------------------------------------------- |
| **`default`** only             | Single API or service. One image, one set of secrets.                                 |
| **`default` + `mirror`**       | The mirror preview points at a more production-like configuration — bigger seed data, real third-party sandboxes. Used by scenarios that need higher fidelity. |
| **`api` + `worker` + `db`**    | Multi-service apps. Each scenario picks the preview that matches the code it touches. |
| **`light` + `heavy`**          | Light boots fast and is used by most scenarios. Heavy boots slow but covers integration tests that need full setup. |

Scenarios target a preview by name. If no name is set, scenarios run against `default`.

### When previews are used

Previews only spin up when the verification run includes at least one **scenario** criterion. If every criterion is handled by [code-scan or invariants](how-verification-works.md), no preview is built and the run finishes faster.

This matters for cost: previews are the expensive part of verification. The classifier minimizes their use by routing structural assertions away from scenario verifiers when possible.

### Reviewer access

The review document exposes a per-run "Open preview" link. Clicking it gives the reviewer access to the same ephemeral container the scenarios ran against — same data, same configuration, same code under test.

The link expires when the preview is torn down (default: shortly after the run completes; configurable). If you need a long-running review session, ask Aviator to extend the preview from the UI.

### Previews vs. CI environments

Previews look like CI environments but they're not the same thing:

|                    | Preview                              | CI environment                        |
| ------------------ | ------------------------------------ | ------------------------------------- |
| Triggered by       | Verify run                           | Push, PR open, schedule               |
| Lifespan           | Per run (minutes)                    | Per job (minutes to hours)            |
| Configured in      | `aviator/verify.yaml`                | CI provider config (GH Actions, etc.) |
| Used by            | Scenario runner + reviewer           | Test runners, build pipeline          |
| Purpose            | Make behavioral verification real    | Run the test suite, ship artifacts    |

If your CI already builds and pushes a container image, that's the image to point your preview at. Verify doesn't replace CI — it consumes its output.

### Skills and previews

Skills are per-preview, not per-repo. A repo with `default` and `mirror` previews can carry different `SKILL.md` files for each. The scenario runner loads the skills tied to the preview it's running against.

→ [Writing a SKILL.md](../how-to-guides/writing-a-skill-md.md)

### See also

* [Preview YAML reference](../reference/preview-yaml.md) — the schema
* [Creating a preview](../how-to-guides/creating-a-preview.md) — walkthrough
* [Managing previews](../how-to-guides/managing-previews.md) — bake vs. setup, refresh, cleanup
* [Seed data for previews](../how-to-guides/seed-data-for-previews.md) — fixtures and deterministic state
