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
| **Exempt paths**    | Glob patterns. PRs touching only these skip verification | Empty   |
| **Preview block**   | Per-repo preview configuration in `aviator/verify.yaml`  | None    |

See [Configuration reference](../reference/configuration-reference.md) for the full surface, and [Preview YAML](../reference/preview-yaml.md) for the preview block.

#### 4. Verify the connection

To confirm everything works:

1. Install the [Aviator MCP](../reference/mcp-tools.md) in your coding agent.
2. Make a small change in this repo with the agent.
3. Have the agent call `submit-spec` through the MCP.
4. Open the review URL returned by the tool — you should land on the review document for this repo.
5. Verify the Aviator Verify check appears on the PR (assuming you've opened one).

If the check doesn't appear or the review document fails to load, see [Fixing verification failures](fixing-verification-failures.md).

### Adding more repositories

Repeat steps 2-3 for each repository you want to enable.

If a repository doesn’t appear in the dropdown, check that:

* The Aviator GitHub App has access to it
* You have admin access to the repository

### Removing a repository

Go to **Verify → Repositories**, find the repository, and click **Remove**.

This disables Verify for that repository. Existing submissions and verification history are retained.

### See also

* [Configuring branch protection](configuring-branch-protection.md)
* [GitHub integration](../reference/github-integration.md)
* [Configuration reference](../reference/configuration-reference.md)
