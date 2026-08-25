---
description: >-
  How Aviator's AI agents handle your code and data — which models we use, where
  code is processed, what we store, and how long we keep it.
---

# Aviator Agents Data Usage & Retention Policy

This policy covers the AI agents behind [Runbooks](../../runbooks/README.md) (agentic code changes) and [Verify](../../verify/README.md) (intent-based verification), and any other Aviator feature that sends your data to a large language model.

_Last updated: August 2026._

## Models used

Aviator uses **Anthropic Claude models exclusively**, called through the Anthropic API. We do not send your data to any other model provider.

Current defaults:

| Where | Model |
| ----- | ----- |
| Runbook execution and Verify agents (Claude Code sessions in the sandbox) | Claude Sonnet (`sonnet` alias, currently Claude Sonnet 5) |
| Verify pipeline stages — invariant selection, evidence collection, evaluation, onboarding drafts | Claude Sonnet 5 |
| Synthesis and ranking passes — docs extraction polish, PR-comment distillation | Claude Opus 5 |

Model versions move as Anthropic ships new ones. Treat this table as the current state, not a contractual pin. Self-hosted operators can pin the agent model themselves with `CLAUDE_CODE_DEFAULT_MODEL`.

## Where your code is processed

Agents never run inside Aviator's application servers. Every run gets a sandbox:

* **Cloud sandboxes** — single-tenant, spawned per task, available in US and EU regions. Your repository is cloned in over a short-lived, scoped token. Nothing is shared between tasks or customers. See [Cloud sandboxes](../../runbooks/concepts/cloud-sandboxes.md).
* **SSH sandboxes** — you host the runner in your own infrastructure. Your code never leaves your network; only the model prompts go to the Anthropic API. See [SSH sandboxes](../../runbooks/concepts/ssh-sandboxes.md).

Aviator does not maintain a persistent copy of your repository. The working checkout exists only for the life of the sandbox.

## What we store, and for how long

| Data | Where | Retention |
| ---- | ----- | --------- |
| Runbook and Verify artifacts — intent, spec, plan, steps, acceptance criteria | Aviator database | Life of the account |
| Agent session transcript — prompts, agent responses, and tool calls, which can include excerpts of your code | Aviator database | 1 year |
| Sandbox workspace — full checkout, build output, agent scratch files | Sandbox only | Destroyed with the sandbox; paused sandboxes are deleted after 7 days of inactivity |
| Verification records — criteria, verdicts, reviewer decisions, waivers | Aviator database | Life of the account (immutable audit records) |
| Verification evidence — diff and AST snippets, screenshots, console logs, API responses, run traces | Object storage, encrypted at rest | 2 years |
| Invariant sources — PR review comments and contributor docs fetched for invariant mining | Object storage, encrypted at rest | Life of the account, refreshed on each mining run |
| Operational logs — task metadata, timings, errors | Logging platform | 30 days |

Verification records are deliberately durable: they are the audit chain reviewers and auditors rely on. See [Audit trails and compliance](../../verify/concepts/audit-trails-and-compliance.md).

To delete an account's agent data ahead of these windows, email [security@aviator.co](mailto:security@aviator.co).

## Data usage guidelines

**No training on your data.** Neither Aviator nor Anthropic uses your prompts, code, or conversations to train or fine-tune models. See [Anthropic's API data retention policy](https://privacy.anthropic.com/en/articles/7996875-can-you-delete-data-that-i-sent-via-api).

**Ownership and copyright.** You own the output the agents produce. Anthropic's API carries copyright indemnity comparable to GitHub Copilot's — see [Anthropic's expanded legal protections](https://www.anthropic.com/news/expanded-legal-protections-api-improvements).

**Least privilege.** Agents act through the repository access you grant the Aviator GitHub App, scoped to the repositories you enable. See [GitHub App permissions](../github-app-permissions.md).

**Secrets.** Secrets you add for sandbox use are encrypted at rest, injected as environment variables at runtime, and cannot be read back in clear text.

## Self-hosted deployments

On-premise installs run the application, database, and (with SSH sandboxes) the agent runners entirely in your infrastructure. The only outbound path is the Anthropic API call, using your own API key. Retention windows above describe Aviator Cloud; in a self-hosted install, retention is whatever your database and log policies say it is.

## Questions

Email [security@aviator.co](mailto:security@aviator.co) for security questions or to request our SOC 2 report.
