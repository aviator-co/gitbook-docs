# Getting started

Runbooks is a multiplayer AI-coding platform that helps you perform consistent, reliable code changes across your repositories. Whether you're managing product backlog, upgrading dependencies, refactoring code patterns, or implementing organization-wide changes, Runbooks streamlines the process with intelligent automation.

### 1. Installation

This setup guide will walk you through the initial set up for Runbooks. If you plan to use the self-hosted option, please contact [howto@aviator.co](mailto:howto@aviator.co).

1. Create an account: [https://app.aviator.co/auth/login](https://app.aviator.co/auth/login)
2. Follow the onboarding flow to connect to the Aviator GitHub app, authorize one or more repositories that you want to use with Runbooks. You can always add more repositories later.

{% hint style="info" %}
If you have trouble connecting the app, please read the [troubleshooting doc](../manage/faqs/troubleshooting-github-app-connection.md).
{% endhint %}

3. Go to Runbooks Config to customize the [Sandbox](https://app.aviator.co/runbooks/settings/sandbox) and [Claude code rules](https://app.aviator.co/runbooks/settings) as needed. By default, Aviator uses cloud sandboxes.

### 2. Access Runbooks

Navigate to the **Runbooks** section in your Aviator dashboard. You'll see two options:

* [**Chat Interface**](https://app.aviator.co/runbooks): Where you describe what you want to accomplish
* [**Runbooks Library**](https://app.aviator.co/runbooks/templates): Your collection of published runbooks

### 3. Create your first Runbook

#### Option A: Start from Scratch

<figure><img src="../.gitbook/assets/Screenshot 2025-10-28 at 11.35.31 AM.png" alt=""><figcaption></figcaption></figure>

1. Click on the chat interface at the top
2.  Describe your migration goal in natural language:

    ```
    I want to upgrade all React components from class-based to
    functional components with hooks.
    ```
3. The AI agents will analyze your request and create a structured runbook
4. You can also choose “**One shot**” for smaller and descriptive task for agents to perform uninterrupted.

#### Option B: Use a Template

1. Navigate to [**Library**](https://app.aviator.co/runbooks/templates) in the runbooks section
2. Browse available templates by category. For example:
   * **Framework Upgrades** (React, Angular, Vue.js)
   * **Language Migrations** (Python 2→3, JavaScript→TypeScript)
   * **Security Updates** (Dependency vulnerabilities, security patterns)
   * **Code Quality** (Linting rules, formatting standards)
3. Select a template and customize it for your needs

#### Option C: Using Slack

<figure><img src="../.gitbook/assets/Screenshot 2025-10-28 at 11.46.02 AM.png" alt="" width="563"><figcaption></figcaption></figure>

1. Make sure [Slack integration is enabled](../api/personal-integrations.md) in settings
2. Describe your task in a single line or use a thread for longer description. The Aviator bot reads the entire thread. Simply say `@Aviator oneshot <reponame> <task description>`
3. The AI agents will analyze your request, and run it in oneshot mode. You will also get a Runbooks link to access the plan and progress.

### 4. Execute and monitor

While generating the plan, the agents may ask some clarifying questions to gather enough requirements. Once the plan steps are generated:

1. Review the planned changes before execution
2. [Invite your team members](concepts/collaborating-with-the-team.md) and other stakeholders to provide feedback
3. Run the runbook step by step or all at once

### 5. Review the code

Once execution is complete for one or more steps, the PRs will be generated. At this point you can review the code like you would a normal PR directly through GitHub.

1. Submit a review
2. Post `/aviator revise` either on a thread, or on the review for Aviator to pick revise the PR based on the comments.
3. You can go back and forth with the agents requesting multiple iterations to improve the code. Once you are satisfied with the changes, you can merge the PR manually or using [MergeQueue](https://docs.aviator.co/mergequeue).

### 6. Publishing a template

If this is a common workflow that can be applied to other parts of the repository or a different repository (e.g. a new log format, or upgrading a react component), you can publish this Runbook as a template. Once published, this will now be available for other developers to use. You can also perform batch operations using this template. Learn more about templates.

To publish a template, simply go to the actions menu on top right, and click “Publish as template”.

Alternatively, if this Runbooks is complete and will not be used again, you can also archive it.

### Advanced Features

#### Custom Personas

Create specialized [personas](concepts/personas.md) for different types of work:

* **Security Expert**: Focuses on security implications
* **Performance Optimizer**: Emphasizes performance improvements
* **Accessibility Specialist**: Ensures accessibility compliance

#### MCP Server Integration

Connect external tools and services:

* **Code analysis tools** (SonarQube, CodeClimate)
* **Testing frameworks** (Jest, Cypress, Playwright)
* **Documentation systems** (Confluence, Notion)

#### Batch Processing

Scale your operations:

* **Template-based batches**: Apply the same template to multiple repos
* **Progressive rollouts**: Gradually deploy changes across teams
* **Dependency management**: Handle complex repository relationships

### Getting Help

* **Community**: [Ask help on Discord](https://discord.gg/MmQWrY9xrA)
* **Support**: Email us for assistance: [_howto@aviator.co_](mailto:howto@aviator.co)

### Security and Compliance

Runbooks includes several security features:

* **Audit trails**: Complete history of all changes
* **Access controls**: Role-based permissions
* **Secure execution**: Isolated sandbox environments
* **Data protection**: Encrypted storage and transmission

Email **security@aviator.co** for any security questions or to request our SOC2 report.

### Billing and Usage

Runbooks usage is tracked and billed based on:

* **Execution time**: Runtime in sandbox environments
* **LLM usage**: How many tokens are used by the LLM provider

Monitor your usage in the billing section of your workspace settings.

### Next Steps

Ready to get started? Here are some recommended first steps:

1. **Explore Templates**: Browse the template library for inspiration
2. **Try a Simple Migration**: Start with a low-risk change like updating documentation
3. **Join the Community**: Share your templates and learn from others
4. **Invite Your Team**: Runbooks are more useful as a multiplayer tool

### See also

* [one-shot-mode.md](concepts/one-shot-mode.md "mention")
* [runbook-format.md](concepts/runbook-format.md "mention")
* [templates.md](concepts/templates.md "mention")

