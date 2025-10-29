# PR Lifecycle management

Runbooks provides three distinct workflows for managing pull request creation during code migrations. Organizations can configure a default workflow, and users can override this setting per chat session.

### Workflow configuration

#### Organization defaults

Administrators configure the default PR workflow in the Runbooks settings. This applies to all new chat sessions unless overridden by individual users.

#### Per-session overrides

Users can modify the PR workflow for their current chat session through the settings popup on the main chat screen. This setting persists for the duration of the session and overrides the organization default.

### Available workflows

#### Stacked PRs (default)

The system automatically creates a separate pull request for each completed step. Each PR builds upon the previous one, creating a stack of dependent changes.

**Behavior**: Step execution triggers immediate PR creation without user intervention. The next step begins automatically on a new branch that includes the previous step's changes.

**Use case**: Complex migrations where each step represents a logical unit that benefits from individual review and testing.

#### Single PR

All changes from multiple steps are consolidated into a single pull request. The initial step creates the PR, and subsequent steps add commits to the same branch.

**Behavior**: After the first step completes, a PR is created. All following steps commit directly to this branch, updating the existing PR with new changes. The PR description automatically updates to reflect step progress.

**Use case**: Cohesive migrations where the complete change set should be reviewed and tested together.

#### Manual approval

The system pauses after each step completion and requests user approval before creating a PR. Users control exactly when and how PRs are created.

**Approval dialog options**: When subsequent steps are queued, users can create a stacked PR, continue on the same branch, continue on a new branch, or cancel remaining steps. When no steps remain queued, users can create a PR or dismiss the dialog.

**Branch selection**: When manually executing a new step without creating a PR for the previous step, users choose whether to continue on the current branch or start a new one.

**Use case**: Teams requiring explicit approval for PR creation or complex workflows that need case-by-case decision making.

### Workflow execution details

#### Stacked PRs execution

Each step creates a new branch based on the previous step's branch. The system generates a PR immediately upon step completion. Subsequent steps automatically begin on branches that include all previous changes, maintaining the dependency chain.

#### Single PR execution

The first step creates both a feature branch and an initial PR. Subsequent steps execute on the same branch, adding commits that appear in the existing PR. The PR description updates automatically to document which steps have completed and their associated changes.

#### Manual approval execution

After each step completes, execution halts and presents a review dialog. The dialog includes a link to view the diff in GitHub. User decisions determine whether to create a PR, how to handle branching for the next step, or whether to continue the migration at all.

For steps executed without prior PR creation, the system prompts for branch selection before beginning execution. Users specify whether the new step should build on existing changes or start fresh.

### Branch management

#### Naming conventions

Stacked PRs use sequential branch names that reflect the step hierarchy. Single PR workflows use a single descriptive branch name for the entire migration. Manual workflows allow users to influence branch creation through their approval decisions.

#### Dependency tracking

The system maintains awareness of which changes depend on others, ensuring that stacked PRs preserve the correct order and that single PRs include all necessary context. Manual workflows rely on user decisions to maintain logical change groupings.

#### Conflict resolution

When conflicts arise between branches, the system provides clear indication of the conflict location and affected files. Users can resolve conflicts through their normal Git workflow before continuing with the migration.

### Integration considerations

#### CI/CD pipelines

Each workflow type triggers CI/CD differently. Stacked PRs generate separate pipeline runs for each PR, allowing incremental validation. Single PRs run the complete test suite against the consolidated changes. Manual workflows depend on when users choose to create PRs.

#### Code review processes

Stacked PRs enable reviewers to examine changes incrementally, with each PR building understanding for the next. Single PRs require reviewers to understand the complete migration in one review cycle. Manual workflows allow teams to align PR creation with their specific review practices.

#### Deployment strategies

Teams using feature flags or gradual rollouts may prefer stacked PRs for incremental deployment. Teams requiring atomic deployments typically choose single PRs. Manual workflows provide flexibility to align with existing deployment processes.



### See also

* [using-stacked-prs.md](using-stacked-prs.md "mention")
* [editing-runbooks.md](editing-runbooks.md "mention")
