# Connect a repository

This guide explains how to connect a GitHub repository to Aviator Verify.

### Prerequisites

* An Aviator account
* Admin or owner access to the GitHub repository
* The Aviator GitHub App installed on your organization

### Steps

#### 1. Install the GitHub App (if not already installed)

Go to **Settings → Integrations → GitHub** in your Aviator dashboard.

If you see "Not connected," click **Connect GitHub** and follow the OAuth flow to install the Aviator GitHub App on your organization.

Select which repositories the app can access:

* **All repositories** — Aviator can access any repo in your org
* **Select repositories** — Choose specific repos

#### 2. Enable the repository for Verify

Go to **Verify → Repositories**.

Click **Add Repository**.

Select the repository from the dropdown. Only repositories the GitHub App can access will appear.

Click **Enable**.

#### 3. Configure repository settings

After enabling, two per-repo toggles are available:

| Setting                       | Description                                                | Default |
| ----------------------------- | ---------------------------------------------------------- | ------- |
| **Enable Verify**             | Run verification on PRs in this repo                       | On      |
| **Enable baseline invariants** | Compose matching invariants into each verification run    | On      |
| **Auto-create runbook on PR** | Automatically create a Verify runbook when a PR is opened | Off     |

Additional repo settings (require-specs gating, exempt paths, merge-blocking enforcement) are on the roadmap.

#### 4. Verify the connection

To confirm everything works:

1. Create a test spec in Verify
2. Push a branch to the repository
3. Link the spec to a PR
4. Check that the `aviator/verify` check appears

If the check doesn't appear, the most common cause is that the Aviator GitHub App doesn't have access to the repository. Confirm in **GitHub → Organization Settings → Installed GitHub Apps → Aviator → Configure**.

### Adding more repositories

Repeat steps 2-3 for each repository you want to enable.

If a repository doesn't appear in the dropdown, check that:

* The Aviator GitHub App has access to it
* You have admin access to the repository

### Removing a repository

Go to **Verify → Repositories**, find the repository, and click **Remove**.

This disables Verify for that repository. Existing specs and verification history are retained.

### See also

* [Reference: GitHub integration](../reference/github-integration.md)
* [Reference: Configuration](../reference/configuration-reference.md)
