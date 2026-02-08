# Configuring branch protection

## How to

Require Aviator Verify to pass before PRs can merge by adding it to your GitHub branch protection rules.

### Prerequisites

* Admin access to the GitHub repository
* Aviator Verify enabled for the repository

### Steps

#### 1. Open branch protection settings

In GitHub, go to your repository’s **Settings → Branches**.

Click **Add rule** or edit an existing rule for your protected branch (usually `main`).

#### 2. Require status checks

Under “Protect matching branches,” enable:

```
☑ Require status checks to pass before merging
```

#### 3. Add Aviator Verify as required check

In the search box, type “Aviator” and select:

```
☑ Aviator Verify
```

This makes verification a required check. PRs cannot merge until verification passes.

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

1. Aviator Verify check appears
2. Merge button is disabled until verification passes
3. If verification fails, the PR cannot be merged (without admin override)

### Bypassing verification

Admins can bypass branch protection for emergencies. This is logged in both GitHub and Aviator audit trails.

To allow admin bypass:

```
☑ Allow specified actors to bypass required pull requests
```

Add users or teams who should have bypass ability.

### Multiple required checks

You likely have other required checks (CI, tests, linting). Aviator Verify works alongside them:

```
Required checks:
☑ ci/build
☑ ci/test
☑ Aviator Verify
```

All checks must pass for the PR to merge.

### See also

* How to connect a repository
* Reference: GitHub integration
