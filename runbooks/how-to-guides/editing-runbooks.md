# Editing Runbooks

Runbooks can be edited in three main ways:

1. **Chat-based editing** - Use natural language prompts to modify Runbooks through the AI agent
2. **Manual full Runbook editing** - Edit the entire [Runbook structure](../concepts/runbook-format.md) and content directly
3. **Individual step editing** - Modify specific steps within a Runbook

Each approach has its own advantages depending on your needs and the complexity of changes you want to make.

### Chat-based editing with prompts

The easiest way to modify a runbook is by providing natural language instructions to the AI agent through the chat interface. This method is ideal for making conceptual changes or adding new functionality without diving into the technical details.

#### How to use chat-based editing

1. Navigate to your runbook in the Aviator interface
2. Open the chat panel or find the "Edit with AI" option
3. Describe the changes you want to make in natural language
4. Review the proposed changes and approve them

<figure><img src="../../.gitbook/assets/Screenshot 2025-10-28 at 3.27.53 PM.png" alt="Chat prompt to update Runbook"><figcaption></figcaption></figure>

#### Example prompts for editing

Here are some effective ways to request runbook modifications:

**Adding new steps:**

* "Add a step to run unit tests before deployment"
* "Include a code review reminder at the beginning"
* "Add validation to check if all environment variables are set"

**Modifying existing logic:**

* "Change the deployment target from staging to production"
* "Update the notification channel to #releases instead of #general"
* "Modify the approval process to require two reviewers instead of one"

**Conditional modifications:**

* "Only run the deployment step if all tests pass"
* "Add a rollback step that triggers if deployment fails"
* "Skip the documentation update for hotfix branches"

**Integration changes:**

* "Add GitHub status checks integration"
* "Include Jira ticket updates in the workflow"

#### Best practices for prompts

* **Be specific**: Instead of "make it better," explain exactly what you want to achieve
* **Provide context**: Mention the current behavior and desired outcome
* **Use examples**: If you have specific commands or configurations in mind, include them
* **Test incrementally**: Make one change at a time to ensure each modification works as expected

### Editing individual steps

When you need to make targeted changes to specific parts of your Runbook, individual step editing provides a focused approach without affecting the rest of the workflow.

#### Selecting a step to edit

1. Open your Runbook in the main view
2. Find the step you want to modify in the steps list
3. Click the edit icon (pencil) next to the step name
4. The step editor will open in a focused view

<figure><img src="../../.gitbook/assets/Screenshot 2025-10-28 at 3.31.09 PM.png" alt="Editing a step"><figcaption></figcaption></figure>

#### Step editor interface

Editing a step gives you the raw markdown that you can edit. Please remember the first line in the raw markdown represents the title of the step.

<figure><img src="../../.gitbook/assets/Screenshot 2025-10-28 at 3.32.41 PM.png" alt=""><figcaption></figcaption></figure>

#### Common step modifications

**Updating commands:**

* Modify shell commands or scripts
* Change command-line arguments
* Update file paths or resource references

**Adjusting conditions:**

* Modify when the step should execute
* Update success/failure criteria
* Change timeout values

**Managing dependencies:**

* Add or remove dependencies on other steps
* Modify input requirements
* Update output specifications

**Configuring notifications:**

* Change notification recipients
* Modify message templates
* Update delivery channels

### Manual full Runbook editing (not recommended)

For complex modifications or when you need precise control over the runbook structure, manual editing gives you complete access to the Runbook's markdown to edit.

{% hint style="info" %}
Note that full raw markdown is not recommended as it can cause parsing errors and may leave your Runbook non-executable.
{% endhint %}

#### Accessing the runbook editor

1. Open your Runbook from the main dashboard
2. Click the "Markdown",  tab and click "Edit".
3. The full editor will open, showing the complete Runbook structure as raw text.

<figure><img src="../../.gitbook/assets/Screenshot 2025-10-28 at 3.29.19 PM.png" alt=""><figcaption></figcaption></figure>

Please familiarize yourself with [Runbooks format](../concepts/runbook-format.md) before editing the full markdown.

### Versioning

Aviator maintains the historic versioning of all Runbooks. The versioning is sequential in increasing order and you can rollback to an older version of Runbook at any time. Even after rolling back to the previous version, a subsequent version would still represent the next incremental number than the highest sequential version.

### See more

* [runbook-format.md](../concepts/runbook-format.md "mention")
* [best-practices-for-planning.md](best-practices-for-planning.md "mention")
