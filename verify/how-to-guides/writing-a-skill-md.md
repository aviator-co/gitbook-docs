# Writing a SKILL.md

A **SKILL.md** is a short context file the scenario runner reads before executing scenarios against your preview. It's how you tell the agent the things it can't infer from your code — test credentials, base URLs, fixture names, gotchas.

You write SKILL files per preview, not per repo. Different previews (staging, sandbox, prod-mirror) often need different context — and they should carry their own.

### Where SKILL files live

Each preview definition in `aviator/verify.yaml` can declare a `skills_dir`. Every `.md` file under that directory is loaded as context for scenarios that run against that preview.

<figure><img src="../../.gitbook/assets/verify-skill-context.svg" alt="A preview's skills_dir resolves to a set of SKILL files loaded by the scenario runner"><figcaption><p>Each preview carries its own skill set. The runner loads all of them before running scenarios.</p></figcaption></figure>

```yaml
preview:
  - name: default
    image: registry.io/api:edge
    port: 8000
    skills_dir: .aviator/skills
    secrets:
      - DB_PASSWORD
      - STRIPE_KEY
```

A single `SKILL.md` at the root of `skills_dir` is the common case. For larger projects, split by domain — `AUTH.md`, `PAYMENTS.md`, `FIXTURES.md` — and the runner loads all of them.

### What to include

Aim for the smallest set of facts that lets the agent run a scenario without trial and error. The categories that matter most:

| Category               | What to write                                                                                  |
| ---------------------- | ---------------------------------------------------------------------------------------------- |
| **Base URL and ports** | "The API is served at `http://localhost:8000`. WebSocket endpoint at `/ws`."                   |
| **Auth setup**         | How to obtain a token in this preview — test users, login endpoint, hard-coded sandbox tokens. |
| **Test users / orgs**  | Named accounts that exist in the seeded data. What plan they're on, what they can access.      |
| **Fixtures**           | Where seed data lives, named IDs the agent can reference, how to reset state between scenarios. |
| **Side effects**       | What's mocked vs. real. "Stripe runs in test mode — no real charges. Email sends are dropped." |
| **Gotchas**            | Things that bit a previous run. "First request after boot takes ~3s due to JIT warmup."        |

The test is: would a new engineer joining the team need to ask someone these things before they could write a sensible test? If yes, put it in SKILL.md.

### What to leave out

The agent already has access to the codebase. Don't repeat what it can read:

* **Architecture descriptions.** "We use Express with Postgres" — the agent can see this. Don't restate it.
* **Endpoint catalogs.** It will discover endpoints from the router. You don't need to list them.
* **Code conventions.** That's invariants, not skills. ([Invariants](../concepts/invariants.md))
* **Implementation history.** "We used to use library X, switched to Y in 2024." Irrelevant for running scenarios.
* **The task at hand.** That's the intent, submitted via MCP per change.

A bloated SKILL.md hurts more than a thin one. Every irrelevant line dilutes the context and slows the agent down.

### Examples

**Minimal SKILL.md for a typical API service:**

```markdown
# API preview

The API runs at http://localhost:8000.

## Auth
- Get a token: POST /auth/login with `{"email": "test@aviator.dev", "password": "test"}`
- Pass it as `Authorization: Bearer <token>` on every other request.

## Test data
- Org `acme` (slug) exists with the `pro` plan.
- User `test@aviator.dev` is an admin of `acme`.
- User `member@aviator.dev` is a non-admin member of `acme`.

## Reset
- Hit `POST /__test__/reset` to drop user-created records and re-seed.
- Reset is allowed because this preview's `STRIPE_KEY` is a test key.
```

That's enough for most scenarios. Notice what's missing: no architecture, no endpoint list, no schema, no history.

**Split skills for a multi-domain service:**

```
.aviator/skills/
├── SKILL.md       # base URL, auth, reset
├── PAYMENTS.md    # Stripe test keys, idempotency, currency handling
└── FIXTURES.md    # detailed seed data, named records
```

`PAYMENTS.md` carries the payments-specific facts so the auth and fixtures skills stay focused. Files don't need to be named after the runner — split by what's coherent to read in one sitting.

**Skill for a preview that talks to a third party:**

```markdown
# Stripe sandbox

Stripe runs in test mode. Every webhook is signed with a fixed test secret
(in env as STRIPE_WEBHOOK_SECRET).

## Useful test cards
- 4242 4242 4242 4242 — succeeds
- 4000 0000 0000 0002 — declined (generic)
- 4000 0027 6000 3184 — requires 3DS

## Replaying webhooks
- POST /__test__/stripe/replay/<event_id> re-fires a stored event.
- Stored events live under tests/fixtures/stripe-events/.
```

### Tips

* **Keep each file short.** If a SKILL file passes ~150 lines, split it.
* **Lead with the facts that change behavior.** Auth and test data first; gotchas last.
* **Use stable identifiers.** Reference fixtures by name (`org "acme"`), not by ID. IDs change when seed data is regenerated.
* **Update the skill when you change the seed.** Stale skills produce confidently wrong scenarios.
* **Don't worry about formatting.** The agent reads them as plain text. Standard markdown is enough — no special syntax required.

### See also

* [Concepts: Invariants](../concepts/invariants.md) — for rules that apply across changes
* [How Verify works](../how-it-works.md) — where skills fit in the verification pipeline
