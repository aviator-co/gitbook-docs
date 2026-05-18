# Configuring branch protection

Make Aviator Verify a required GitHub status check so PRs cannot merge until verification passes. This is a standard GitHub branch-protection configuration; the gate is enforced by GitHub, not by Aviator.

### Prerequisites

* Admin access to the GitHub repository
* Aviator Verify enabled for the repository
* At least one verification run has completed on the repo (so GitHub recognizes the check name)

### Steps

#### 1. Open branch protection settings

In GitHub, go to your repository's **Settings → Branches**.

Click **Add rule** or edit an existing rule for your protected branch (usually `main`).

#### 2. Require status checks

Under "Protect matching branches," enable:

```
☑ Require status checks to pass before merging
```

#### 3. Add Aviator Verify as a required check

In the search box, type "aviator" and select:

```
☑ aviator/verify
```

This makes verification a required check. GitHub will not allow merge until verification passes.

#### 4. Optional: Require branches to be up to date

Enable this option if you want PRs to be based on the latest target branch:

```
☑ Require branches to be up to date before merging
```

This helps avoid merge conflicts but can create more CI load.

#### 5. Save changes

Click **Save changes** at the bottom of the page.

### Testing the configuration

Create a PR with a linked spec. You should see:

1. The `aviator/verify` check appears
2. The merge button is disabled until verification passes
3. If verification fails, the PR cannot be merged (unless an admin overrides)

### Bypass and override

GitHub's standard branch-protection bypass mechanisms apply. Aviator does not currently track or record bypass events — if you need that, GitHub's audit log is the source of truth.

### Multiple required checks

You likely have other required checks (CI, tests, linting). Aviator Verify works alongside them:

```
Required checks:
☑ ci/build
☑ ci/test
☑ aviator/verify
```

All checks must pass for the PR to merge.

### See also

* [How to connect a repository](connect-a-repository.md)
* [Reference: GitHub integration](../reference/github-integration.md)
