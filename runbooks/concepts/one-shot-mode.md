# One shot mode

[One-shot mode](../how-to-guides/one-shot-mode.md) is an automated execution mode for Runbooks that streamlines the entire code modification workflow into a single, uninterrupted process. When enabled, Runbooks generates a complete execution plan and immediately begins implementing all steps without requiring manual approval or intervention between steps.

<figure><img src="../../.gitbook/assets/Screenshot 2025-10-14 at 11.43.10 AM.png" alt="one-shot mode"><figcaption></figcaption></figure>

#### When to Use One-Shot Mode

One-shot mode is ideal for:

* **Well-defined tasks** where requirements are clear and unambiguous
* **Automated migrations** such as dependency upgrades, API version updates, or batch refactoring
* **Repetitive operations** that follow established patterns in your codebase
* **Quick fixes** where the scope is limited and the approach is straightforward
* **Time-sensitive changes** that need to be implemented quickly without multi-step review cycles

#### When NOT to Use One-Shot Mode

Avoid one-shot mode for:

* **Complex refactoring** that may require architectural decisions or mid-execution adjustments
* **Exploratory tasks** where you're unsure of the full scope or implementation approach
* **High-risk changes** to critical systems that require careful review at each stage
* **Tasks with unclear requirements** that might benefit from clarifying questions

#### Key Differences from Standard Mode

| Aspect                     | Standard Mode                                   | One-Shot Mode                                        |
| -------------------------- | ----------------------------------------------- | ---------------------------------------------------- |
| **Requirements gathering** | Asks clarifying questions                       | Skips clarification, makes reasonable assumptions    |
| **Plan approval**          | Waits for user to approve the runbook           | Automatically begins execution after generation      |
| **Execution control**      | User chooses when to execute each step          | All steps execute automatically in sequence          |
| **Pull request strategy**  | Creates separate PRs for each major step        | Creates a single draft PR, updates it with each step |
| **User involvement**       | Interactive, allows modifications between steps | Fully automated, monitoring only                     |

### See also

[How to use one-shot mode](../how-to-guides/one-shot-mode.md)
