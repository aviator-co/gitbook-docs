# Seed data for previews

Scenarios are only as good as the state they run against. A preview that boots with no data, or with different data each run, produces verdicts you can't trust. This guide covers the seeding strategies that work.

For where seed work fits in a preview, see [Creating a preview](creating-a-preview.md) and [Managing previews](managing-previews.md).

### Why determinism matters

Verify treats verification as reproducible. If a scenario passes today, it should pass tomorrow on the same code. Non-deterministic seeds break that contract — a scenario might rely on "the first user" or "any active subscription," and which one shows up depends on insertion order.

Three properties to preserve:

* **Known records.** Scenarios should reference data by stable identifiers, not by "find any."
* **Reset between runs.** Every Verify run starts from the same state. State from the previous run never leaks in.
* **Reset between scenarios within a run.** If two scenarios mutate the same record, one shouldn't see the other's changes.

If any of these break, the noise floor of verification rises. Verdicts get less reliable. People start ignoring them.

### Strategy 1: Fixture files in the repo

Best for small, stable datasets. Commit a JSON or SQL file to the repo and load it in `setup`.

```yaml
preview:
  - name: default
    image: ghcr.io/acme/api:edge
    port: 8000
    setup: .aviator/scripts/preview-setup.sh
    secrets:
      - DB_PASSWORD
```

```bash
# .aviator/scripts/preview-setup.sh
#!/usr/bin/env bash
set -euo pipefail
./bin/api migrate
./bin/api seed --fixture=tests/fixtures/preview.json
```

```json
// tests/fixtures/preview.json
{
  "orgs": [
    { "slug": "acme", "plan": "pro" },
    { "slug": "starter-co", "plan": "starter" }
  ],
  "users": [
    { "email": "admin@acme.test", "org": "acme", "role": "admin" },
    { "email": "member@acme.test", "org": "acme", "role": "member" }
  ]
}
```

Reference these by name in your SKILL.md (`org "acme"`, `user "admin@acme.test"`). Scenarios read the skill, use the named records, and produce reproducible verdicts.

**Pros:** Diff-able, reviewable, no external dependencies. **Cons:** Doesn't scale past a few hundred records; tedious to maintain for evolving schemas.

### Strategy 2: Factories at setup time

Best for medium-sized seeds where you want code, not data, in the repo. Run a factory script in `setup`.

```bash
# .aviator/scripts/preview-setup.sh
#!/usr/bin/env bash
set -euo pipefail
./bin/api migrate
./bin/api factory create-org --slug=acme --plan=pro
./bin/api factory create-user --org=acme --email=admin@acme.test --role=admin
./bin/api factory create-subscription --org=acme --plan=pro --status=active
```

Factories work especially well when your test suite already uses them — share the code. Build factories with explicit arguments (`--slug=acme`) rather than randomness, so the seed is the same every run.

**Pros:** Scales further than fixtures, evolves with your schema. **Cons:** Setup time grows linearly with seed size.

### Strategy 3: Anonymized prod snapshot

Best for high-fidelity scenarios — pricing logic, complex permission graphs, performance under realistic data shape. Snapshot prod, scrub PII, store the snapshot, restore it at boot.

```bash
# .aviator/scripts/preview-setup.sh
#!/usr/bin/env bash
set -euo pipefail
./bin/api restore --snapshot=$ANONYMIZED_SNAPSHOT_URL
```

The snapshot lives somewhere persistent (S3, GCS) and is refreshed on a schedule by a separate job that pulls from prod, scrubs, and uploads. The preview just restores.

**Pros:** Realistic shape, finds bugs synthetic data misses. **Cons:** Operational overhead — snapshot pipeline, PII scrubbing, access control on the snapshot.

Most teams don't need this until they're verifying behavior that depends on real data shape (large-tenant queries, pricing on actual subscription distributions). Start simpler.

### Strategy 4: Baked baseline + setup additions

Best for repos that boot fast but need a couple of per-run mutations. Bake the baseline into the image, mutate at setup time.

* **Image build (CI):** runs migrations and seeds the baseline (10k orgs, 100k users from anonymized prod, or whatever your baseline is). This is slow, runs once per image push.
* **Setup script (per run):** applies any migrations new in this PR, adds the named records the scenarios reference.

This combines the speed of pre-baked state with the freshness of per-run additions. It's the right answer once your baseline is too big for setup-time loading but you still need pull-time data.

### Reset between scenarios within a run

If your scenarios mutate state, give them a reset hook so they don't interact.

```bash
# Expose an internal reset endpoint, gated by an env flag the preview sets.
POST /__test__/reset
```

The scenario runner calls `/__test__/reset` between scenarios. The reset truncates user-created tables and re-seeds from the baseline.

Two rules:

* **Gate the reset endpoint.** Only enable when `AVIATOR_TEST_MODE=1` (or equivalent). It must be unreachable in production.
* **Reset to a known state, not an empty one.** Resetting to empty makes every scenario re-create fixtures, which is slow and error-prone.

### Anti-patterns to avoid

* **Random IDs in fixtures.** `user-{{uuid}}` makes scenarios reference moving targets. Use stable slugs.
* **Time-relative data.** "User created 7 days ago" produces different data depending on when verification runs. Use fixed `created_at` values.
* **Implicit dependencies between fixtures.** "Add this user *after* that org" baked into ordering is brittle. Make the dependency explicit (foreign key references).
* **No reset between runs.** Trusting that the previous preview was torn down isn't a strategy. Re-seed every run.

### See also

* [Creating a preview](creating-a-preview.md)
* [Managing previews](managing-previews.md)
* [Writing a SKILL.md](writing-a-skill-md.md) — how scenarios learn what your seed contains
