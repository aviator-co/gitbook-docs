# Templates

Runbook Templates are reusable automation patterns that standardize common coding tasks such as code migration, refactoring, and maintenance work. They provide structured, step-by-step guidance for complex development workflows and can be customized for your specific project needs.

Aviator offers a library full of prebuilt templates, and you can also publish your own templates to be used within your organization.

## What is a Runbook Template?

A Runbook Template is a predefined workflow that contains:

* **Structured markdown content** with hierarchical execution steps
* **Categorization** for easy discovery and organization
* **Reusable patterns** for common development tasks
* **Usage tracking** to identify popular templates

Templates serve as starting points for creating new runbooks, allowing teams to standardize their approach to routine but complex tasks like framework migrations, code modernization, and systematic refactoring.

Each Runbook Template follows the same format as a standard Runbook.

## Prebuilt templates

We offer prebuilt templates for some common coding tasks patterns such as code migration, refactoring, maintenance work, etc.

These prebuilt templates are agnostic of your code base but has some standard learnings based on the tasks. When you use these prebuilt templates, Runbooks agents will modify the Runbook template to adapt to your code base. This provides better quality results because it blends the standard process to perform an action with your code context. You ca

## Custom templates

Custom templates are the Runbooks published by your team manually. These templates are shared across the organization. Anyone can request to publish a template, and these can be available in the library once approved by an admin.

### Creating a template

To create a template, you must have edit access to the Runbook. Simply go to your Runbook and from the actions menu select: `Publish ...` . This will be send a request to the admins, and will show up in pending approval in the library section. Once approved by admins, this will now be available to be reused.

{% hint style="info" %}
Runbooks can be shared and reused even without them being published. Publishing Runbooks help create the templates that may have long term wider usability.
{% endhint %}

## Template categories

Categories makes it easy to organize the templates, and make them searchable. Think of categories as labels that can be associated with the Runbook Templates. Categories can also be added or removed as needed.

Prebuilt templates are organized into the following default categories:

| Category      | Description                                          | Examples                                                      |
| ------------- | ---------------------------------------------------- | ------------------------------------------------------------- |
| react         | React framework related templates                    | Class to hooks migration, state management updates            |
| typescript    | TypeScript migration and type-related templates      | JavaScript to TypeScript conversion, type safety improvements |
| migration     | General migration templates                          | Language upgrades, framework migrations                       |
| refactor      | Code refactoring and systematic improvements         | Code organization, pattern standardization                    |
| setup         | Project setup and configuration templates            | Development environment setup, tooling configuration          |
| tooling       | Development tooling and build system templates       | Build optimization, CI/CD improvements                        |
| test\_quality | Test quality, coverage, and testing best practices   | Test coverage improvement, flaky test fixes                   |
| upgrade       | Framework and dependency upgrade templates           | Package updates, breaking change management                   |
| performance   | Performance optimization and build time improvements | Bundle optimization, runtime performance                      |
| flaky\_test   | Flaky test resolution and test stability             | Test reliability improvements                                 |
| readability   | Code readability, formatting, and documentation      | Code style standardization, documentation updates             |
| misc          | Miscellaneous and other templates                    | Custom workflows, specialized tasks                           |

You cannot modify the categories for pre-built templates but the categories can be modified for all custom templates.

## See also

* [managing-templates.md](../how-to-guides/managing-templates.md "mention")
