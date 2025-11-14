# One shot mode

[One shot mode](../concepts/one-shot-mode.md) is an automated execution mode for Runbooks that streamlines the entire code modification workflow into a single, uninterrupted process.

### Starting a One Shot Session

#### **Via Web Interface**

<figure><img src="../../.gitbook/assets/Screenshot 2025-10-14 at 11.43.10 AM.png" alt=""><figcaption></figcaption></figure>

1. Navigate to the Runbooks dashboard
2. Click "New Runbook"
3. Select your repository
4. Enable the **One shot mode** toggle from the dropdown
5. Describe your task in the input field
6. Click "Create" to start execution

#### **Via Slack**

<figure><img src="../../.gitbook/assets/Screenshot 2025-11-13 at 2.28.54 PM.png" alt="one-shot mode with Slack"><figcaption></figcaption></figure>

Send a message to the Aviator bot using this format:

```
@Aviator oneshot <reponame> <task description>
```

**Example:**

```
@Aviator oneshot my-app/frontend Add an alert status bar when user is out of credits
```

You can also skip the org-name when specifying the repo:

```
@Aviator oneshot frontend Add an alert status bar when user is out of credits
```

### Execution Flow

Once a one-shot session is initiated, the following happens automatically:

#### **1. Runbook Generation**

* Aviator analyzes your codebase to understand the current state
* Generates a comprehensive execution plan with numbered steps
* Documents assumptions made based on code analysis
* No clarifying questions are asked; reasonable defaults are used

#### **2. Automatic Execution**

* All steps begin executing immediately after plan generation
* Steps execute sequentially in dependency order
* Each step generates code changes and commits them

#### **3. Pull Request Management**

* A single **draft PR** is created with the first code-generating step
* Subsequent steps add commits to the same PR
* The PR title reflects the overall task (e.g., `[WIP] Upgrade React from 17 to 18`)
* PR description includes: runbook URL, executor, and progress indication

#### **4. Completion**

* When all steps complete successfully, the draft PR is automatically marked as **ready for review**
* PR title is updated to remove `[WIP]` prefix
* You receive a notification (via chat or Slack) that execution is complete
* The PR is ready for team review and merge

### Monitoring Progress

While one-shot mode runs automatically, you can monitor progress in real-time:

* **Chat interface**: Shows step-by-step execution logs and status updates
* **Pull request**: View incremental commits as each step completes
* **Runbook page**: Displays current step status (queued, in progress, completed)

### Handling Failures

If a step fails during execution:

* Execution **stops** at the failed step
* Remaining queued steps return to "not started" status
* The draft PR remains open with all completed work
* Error details are shown in the chat interface
* You can:
  * Manually fix the issue and re-run the failed step
  * Modify the runbook plan and continue
  * Close the PR if the approach needs reconsideration

### Best Practices

1. **Start with clear task descriptions**: Since one-shot mode skips clarification, provide detailed requirements upfront
   * ✅ Good: "Add TypeScript types to the UserProfile.tsx file"
   * ❌ Vague: "Update components"
2. **Review the generated plan**: Although execution starts automatically, you can view the plan in the chat interface. If it's not what you expected, you can cancel and restart in standard mode.
3. **Use for incremental changes**: One-shot mode works best for tasks that are additive or isolated. For changes that might conflict with ongoing work, consider standard mode with step-by-step review.
4. **Leverage for repetitive tasks**: Once you've validated an approach with standard mode, use one-shot mode for similar tasks across different parts of your codebase.
5. **Monitor the PR**: Even though execution is automatic, review the PR commits to ensure changes align with expectations before merging.

***

### Example Workflows

#### Example 1: Add Type Safety to a Component

**Task**: Add TypeScript types to the Button.tsx component

**Command** (Slack):

```
@Aviator oneshot my-app Add proper TypeScript interfaces to Button.tsx
```

**What happens**:

1. Runbook analyzes the `Button.tsx` file
2. Generates steps: define props interface, add return type, update exports
3. Executes all steps automatically
4. Creates draft PR with the updated component
5. Marks PR ready when complete

#### Example 2: Add Error Handling

**Task**: Add try-catch blocks to the authentication service

**Steps** (via web interface):

1. Enable one-shot mode
2. Enter task: "Add error handling to all methods in auth.service.ts"
3. Aviator generates plan: wrap each method in try-catch, add logging
4. Execution proceeds automatically
5. Single PR contains all error handling changes

#### Example 3: Update Documentation

**Task**: Add JSDoc comments to a utility file

**Command** (Slack):

```
@Aviator oneshot my-repo Add JSDoc comments to utils/formatters.js
```

**What happens**:

1. Runbook analyzes all functions in `formatters.js`
2. Generates plan: add JSDoc for each function with parameters and return types
3. Executes automatically
4. Creates PR with documented code
5. Marks PR ready when complete

### See also

* [One-shot mode concepts](../concepts/one-shot-mode.md)
