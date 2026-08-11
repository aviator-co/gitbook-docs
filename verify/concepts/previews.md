# Previews

A **preview** is an ephemeral environment Verify builds on demand and runs scenarios against. It's how Verify makes behavioral claims real — every "the endpoint returns X" criterion needs the code to actually run somewhere, and the preview is that somewhere.

A preview is short-lived: built for the branch's current commit, used by the scenario runner (and optionally a human reviewer), and torn down when the branch moves to a new commit. A re-run on the same commit reuses the running preview — including its state. See [Lifecycle](#lifecycle) for the exact contract.

### Optional, not required

Previews are optional. Verify works on day one with code-scan alone — without a preview, every criterion is routed to code-scan (static analysis of the diff) and you get verdicts on structural criteria from the first PR.

A preview unlocks **runtime verification**: behavioral criteria like "the endpoint returns 429" or "the modal closes on submit" need the code to actually run, and that requires a preview. Most teams get going with code-scan and add a preview when their criterion list starts asking about behavior the diff alone can't answer.

### Lifecycle

<figure><img src="../../.gitbook/assets/verify-preview-lifecycle.svg" alt="Preview lifecycle: define, build, boot, use, teardown"><figcaption><p>A preview moves through five phases</p></figcaption></figure>

| Phase        | What happens                                                                                |
| ------------ | ------------------------------------------------------------------------------------------- |
| **Define**   | The preview is configured in the Verify settings in the Aviator dashboard (**Verify → Settings → Verify**, with the repo selected). |
| **Build**    | Aviator boots a container from the cached image and prepares the environment.                |
| **Boot**     | The setup script runs. The declared port becomes reachable.                                  |
| **Use**      | Scenarios execute against the preview. Reviewers can also open it from the UI.               |
| **Teardown** | When the branch moves to a new commit (or the preview is stopped), the optional teardown script runs and the container is destroyed. |

Whether a run starts fresh depends on whether the branch has moved. When a verification run starts, Aviator compares the branch's current head SHA to the SHA the existing preview was built from:

* **New commit since the last run** — the old container is torn down and a fresh one is built and booted. The setup script runs. The run starts from a clean slate.
* **Same commit (a re-run)** — Aviator reconnects to the already-running container and skips the setup script entirely. Everything from the previous run persists: database rows, files, logged-in sessions.

The practical consequence: if your scenarios mutate data, a re-run on the same commit sees the previous run's leftovers. Push a new commit (or stop the preview) to force a clean boot.

### Composition

A preview is composed of inputs from three places: a preview image, your secret store, and your repo.

<figure><img src="../../.gitbook/assets/verify-preview-anatomy.svg" alt="Preview anatomy: inputs, the preview container, and what consumes it"><figcaption><p>Where each piece of a preview comes from, and what uses it</p></figcaption></figure>

* **Image** — a preview image registered with Aviator for your account. Aviator caches the image locally and boots containers from it. Register images through **Settings → Sandboxes** in the Aviator UI.
* **Secrets** — runtime secrets (DB passwords, API keys) referenced by name from the account secret store and injected as environment variables when the container boots.
* **Setup script** — optional. Runs after the container starts and before the port is marked ready. Used for migrations, seeding, warm-up.
* **Teardown script** — optional. Runs before destruction to release external resources.
* **Port** — the port the runner connects to. The container is considered ready when this port accepts connections.

Aviator stitches these together into a single ephemeral container. Your Verify settings are the contract — see [Preview YAML reference](../reference/preview-yaml.md) for every field.

### Multiple previews per repo

A repo can have more than one preview configured. Common patterns:

| Pattern                        | Why                                                                                   |
| ------------------------------ | ------------------------------------------------------------------------------------- |
| **`default`** only             | Single API or service. One image, one set of secrets.                                 |
| **`default` + `mirror`**       | The mirror preview points at a more production-like configuration — bigger seed data, real third-party sandboxes. Used by scenarios that need higher fidelity. |
| **`api` + `worker` + `db`**    | Multi-service apps. Each scenario picks the preview that matches the code it touches. |
| **`light` + `heavy`**          | Light boots fast and is used by most scenarios. Heavy boots slow but covers integration tests that need full setup. |

Scenarios target a preview by name. If no name is set, scenarios run against `default`.

### When previews are used

Previews only spin up when the verification run has at least one **runtime** criterion — a criterion the classifier decides needs to be checked against the running code (see [How verification works](how-verification-works.md)). If every criterion can be verified by code-scan alone, no preview is built and the run finishes faster.

This matters for cost: previews are the expensive part of verification. The classifier minimizes their use by routing structural assertions away from the runtime path when possible.

### Reviewer access

The review document exposes a per-run "Open preview" link. Clicking it gives the reviewer access to the same ephemeral container the scenarios ran against — same data, same configuration, same code under test.

The link expires when the preview is torn down (default: shortly after the run completes). If you need a long-running review session, ask Aviator to extend the preview from the UI.

### Previews vs. CI environments

Previews look like CI environments but they're not the same thing:

|                    | Preview                              | CI environment                        |
| ------------------ | ------------------------------------ | ------------------------------------- |
| Triggered by       | Verify run                           | Push, PR open, schedule               |
| Lifespan           | Until the branch moves to a new commit | Per job (minutes to hours)            |
| Configured in      | Verify settings in the Aviator dashboard | CI provider config (GH Actions, etc.) |
| Used by            | Scenario runner + reviewer           | Test runners, build pipeline          |
| Purpose            | Make behavioral verification real    | Run the test suite, ship artifacts    |

### See also

* [Preview YAML reference](../reference/preview-yaml.md) — the schema
* [Creating a preview](../how-to-guides/creating-a-preview.md) — walkthrough
* [Managing previews](../how-to-guides/managing-previews.md) — bake vs. setup, refresh, cleanup
* [Seed data for previews](../how-to-guides/seed-data-for-previews.md) — fixtures and deterministic state
