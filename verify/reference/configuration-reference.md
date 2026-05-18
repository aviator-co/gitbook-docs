---
description: Configuration options for Verify.
---

# Configuration reference

This is a reference for currently-available configuration. Verify is in active development; many configuration knobs commonly expected (require-specs gating, exempt paths, merge-blocking enforcement, scheduled exports, REST API) are on the roadmap but not yet implemented.

### Repository settings

Configured in **Verify → Repositories → \[repo] → Settings**.

| Setting                          | Type    | Default | Description                                                  |
| -------------------------------- | ------- | ------- | ------------------------------------------------------------ |
| `enable_verify`                  | boolean | `true`  | Run verification on PRs in this repo                         |
| `enable_baseline_invariants`     | boolean | `true`  | Compose matching invariants into each verification run       |
| `auto_create_runbook_on_pr_open` | boolean | `false` | Automatically create a Verify runbook when a PR is opened    |

Per-repo file-based configuration (`.aviator/verify.yaml`) is not currently read. All settings live in the dashboard.

### Baseline invariants

Configured in **Verify → Invariants**.

Each invariant has:

| Field          | Description                                              |
| -------------- | -------------------------------------------------------- |
| `name`         | Short identifier                                         |
| `description`  | Plain-language description                               |
| `rule_text`    | The rule the verifier evaluates                          |
| `conditions`   | When the invariant applies (file paths, languages, change types) |
| `status`       | `PENDING` (proposed), `ACTIVE` (enforced), `ARCHIVED`    |

Invariants in `ACTIVE` status are composed into the criteria set for any verification run whose changed files match the invariant's conditions.

### GitHub check

Verify creates a single GitHub check per PR:

| Property      | Value                              |
| ------------- | ---------------------------------- |
| Check name    | `aviator/verify`                   |
| Statuses      | `queued`, `in_progress`, `success`, `failure` |
| Output        | Markdown table of criteria + verdicts |
| Details URL   | Link to the runbook's verify page  |

To require this check before merge, configure GitHub branch protection in the repo settings. Aviator itself does not enforce merge-blocking.

### Onboarding

When a repo is first connected for Verify, an onboarding session walks through:

1. Repo analysis — scan languages, structure, conventions
2. Questionnaire — admin input on team practices and priorities
3. Drafted baseline invariants — LLM-proposed from analysis + questionnaire + docs (CLAUDE.md, CONTRIBUTING.md, etc.)
4. Review — admin approves, edits, or rejects each draft
5. Done — approved invariants become `ACTIVE`

Onboarding state is persisted, so it can be paused and resumed.

### Feature flag

Verify is gated by an account-level feature flag. Contact [support@aviator.co](mailto:support@aviator.co) to enable it for your account.

### Not yet configurable

The following are commonly expected and are tracked as roadmap items:

* Per-repo `require_specs` gating
* `exempt_paths` (skip verification on certain file patterns)
* Merge-blocking enforcement at the Aviator layer
* Self-approval policy (`allow_self_approval`)
* Required-approver count
* Verification timeout overrides
* `.aviator/verify.yaml` file-based configuration
* Scheduled audit exports
* Public REST API for verify operations

If any of these matter to your team, this is design-partner-shaped work — input directly affects which we prioritize.

### See also

* [How to connect a repository](../how-to-guides/connect-a-repository.md)
* [Setting up baseline invariants](../setting-up-org-invariants.md)
