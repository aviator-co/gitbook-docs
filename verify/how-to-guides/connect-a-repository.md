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

| Setting              | Description                             | Default |
| -------------------- | --------------------------------------- | ------- |
| **Require specs**    | PRs must have a linked spec             | Off     |
| **Exempt paths**     | File patterns that don’t need specs     | Empty   |
| **Auto-link specs**  | Link specs to PRs based on branch names | On      |
| **Block on failure** | Failed verification blocks merge        | On      |

Adjust these based on your workflow. For a trial, you might leave “Require specs” off to start.

#### 4. Verify the connection

To confirm everything works:

1. Create a test spec in Verify
2. Push a branch to the repository
3. Link the spec to a PR
4. Check that the Aviator Verify check appears

If the check doesn’t appear, see Troubleshooting: Check not appearing.

### Adding more repositories

Repeat steps 2-3 for each repository you want to enable.

If a repository doesn’t appear in the dropdown, check that:

* The Aviator GitHub App has access to it
* You have admin access to the repository

### Removing a repository

Go to **Verify → Repositories**, find the repository, and click **Remove**.

This disables Verify for that repository. Existing specs and verification history are retained.

### See also

* How to configure branch protection
* Reference: GitHub integration
* Reference: Configuration options
