# Runbooks

Runbooks are the core abstraction for defining and executing code transformations. They encapsulate the complete plan for a code change, from analysis to implementation.

## Runbook Structure

Runbooks use a custom format designed for flexibility and clarity. Typical components include:

* **Metadata**: Version, author, collaborators, creation date
* **Objective**: High-level description of the transformation
* **Analysis**: Code patterns to identify and transform
* **Steps**: Ordered list of actions to perform
* **Validation**: Tests and checks to verify success

The Runbooks in Aviator system must be structured in a specific way, so that these can be processed by the agents:

* **Steps and sub-steps -** Each step should be contained that either requires a specific action (like creating a branch), or otherwise a code change that can be done in a pull request.
* **Top-level context** - context associated with the entire task that can be used for every step in the LLM
* **Step-level context** - context associated with a specific step or a sub-step.

### Steps

Each Runbook is broken down into steps and sub-steps, the goal of each step is to define a very small clearly scoped task that the agents can perform with accuracy. Aviator agents understands these steps and it’s boundaries well, and have proper naming convention with defined rules:

* Each step is a number increment starting from `1`.
* Each substep is prefixed with the step name of the parent, followed by the number. E.g. `1.1` , `1.2`, `1.2.1` ,..
* The sequence must be contiguous
* Each step name must be unique

Runbook is validated on edits based on the formatting rules above.

Agents uniquely understands each step, that way you can ask the agents to modify, reorder or execute a specific step or a sub-step.

### Context

There are several ways to provide context to the agents. Please read the Context Management guidelines for the deep dive. Along with some of the global context that helps agents understand the code base, some additional context may also be provided within runbooks. This would be specific to the task at hand. The context can either be provided before any of the steps in the Runbook, or may be provided within the Runbook specific step or sub-step.

### Runbook Library

Aviator comes prebundled with a library of Runbooks that are tried and tested for specific use cases. Instead of start from scratch, you can start with these Runbooks and add more context for your specific code base. You can also ask agents to analyze the codebase and update the Runbook automatically with the necessary context.

![Runbook Library](<../../.gitbook/assets/Screenshot 2025-07-09 at 3.32.42 PM.png>)

### Sharing and editing

Any active Runbook with a chat session can be shared with other users within your organization to collaborate together. This expands the chat experience inviting other developers to provide feedback and make edits.

There are two ways of editing the Runbook.

**Manually**

Either you can manually update each Runbook by clicking on edit on the right panel within the Runbook view. This opens the editor with raw Markdown to edit. Click Save when done editing. Aviator Runbooks follow a specific pattern and is validated on manual edits.

**Using Agents**

You can also ask the agents to update specific part of all of the Runbook directly in the chat session.

### Versioning

Aviator maintains the historic versioning of all Runbooks. The versioning is sequential in increasing order and you can rollback to an older version of Runbook at any time. Even after rolling back to the previous version, a subsequent version would still represent the next incremental number than the highest sequential version.

