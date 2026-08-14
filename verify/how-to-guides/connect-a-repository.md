# Connect a repository

This guide explains how to connect a GitHub repository to Aviator Verify.

### Prerequisites

* An Aviator account
* Admin or owner access to the GitHub repository
* The Aviator GitHub App installed on your organization

### Steps

#### 1. Install the GitHub App (if not already installed)

Go to **Settings → Integrations → GitHub** in your Aviator dashboard.

If you see “Not connected,” click **Connect GitHub** and follow the OAuth flow to install the Aviator GitHub App on your organization.

Select which repositories the app can access:

* **All repositories** — Aviator can access any repo in your org
* **Select repositories** — Choose specific repos

#### 2. Enable the repository for Verify

Go to **Verify → Repositories**.

Click **Add Repository**.

Select the repository from the dropdown. Only repositories the GitHub App can access will appear.

Click **Enable**.

#### 3. Configure repository settings

After enabling, you can configure:

| Setting             | Description                                              | Default |
| ------------------- | -------------------------------------------------------- | ------- |
| **Auto-create runbook on PR open** | Opening a PR in this repository automatically creates a runbook | Off |
| **Generate invariants from PR comments** | Aviator reviews merged-PR comments weekly to propose new baseline invariants | On after onboarding |
| **`verify.yaml`**   | Per-repo Verify configuration, including preview environments, edited under **Verify → Settings → Verify** | Empty |

A preview is **not required** to get started — Verify runs code-scan only without one and produces verdicts on structural criteria from the first PR. Add a preview later when behavioral verification matters.

See [Configuration reference](../reference/configuration-reference.md) for the full surface, and [Preview YAML](../reference/preview-yaml.md) for the preview block.

#### 4. Set up the repository's coding agents

Enabling the repository tells Aviator to verify it. `aviator init` sets up the other half — the coding agents that capture the intent Verify checks against.

Install the [Aviator CLI](../reference/cli.md), then run this once from a clone of the repository:

```bash
aviator init
```

Choose the repo-wide setup when it asks who it's for, then pick the agents your team uses. It writes the hook configuration into the repository — `.claude/settings.json` for Claude Code, `.codex/hooks.json` for Codex. Commit those files and the setup travels with the repo.

From then on, every agent session in this repository starts with a standing instruction to capture intent and acceptance criteria before opening a pull request, and gets reminded again at the PR itself.

Everyone on the team needs three things:

* The Aviator CLI — `brew install aviator-co/tap/aviator`, or the deb/rpm package on Linux.
* Their own API token, set as `AVIATOR_API_TOKEN` or in `~/.config/aviator/config.yaml` — see [Authentication](../reference/cli.md#authentication). Submissions are attributed to the token's owner, so a shared token collapses the audit trail.
* The `/verify-submit` skill from the [Aviator agent plugins](https://github.com/aviator-co/agent-plugins).

Codex users also need to run `/hooks` inside Codex and trust the hook.

See [Set up agent hooks](set-up-agent-hooks.md) for personal setup, provisioning, and removal.

#### 5. Verify the connection

To confirm everything works:

1. Make a small change in this repo with your coding agent.
2. Let it open a pull request. The standing instruction should prompt it to run `/verify-submit` first — if it doesn't, run `/verify-submit` yourself.
3. Open the session URL the CLI prints — you should land on the review document for this repo.
4. Check that the Aviator Verify check appears on the pull request.

If the hook never fires, start a fresh agent session — the standing instruction is delivered at session start, so a session already running when you ran `init` won't have it. If the check doesn't appear or the review document fails to load, see [Fixing verification failures](fixing-verification-failures.md).

### Adding more repositories

Repeat steps 2-4 for each repository you want to enable. Step 4 is per-repo when you set it up for the whole team, since the hook config is committed to each repository.

If a repository doesn’t appear in the dropdown, check that:

* The Aviator GitHub App has access to it
* You have admin access to the repository

### Removing a repository

Go to **Verify → Repositories**, find the repository, and click **Remove**.

This disables Verify for that repository. Existing submissions and verification history are retained.

### See also

* [Set up agent hooks](set-up-agent-hooks.md)
* [Aviator CLI](../reference/cli.md)
* [Configuring branch protection](configuring-branch-protection.md)
* [GitHub integration](../reference/github-integration.md)
* [Configuration reference](../reference/configuration-reference.md)
