# Step-by-step execution

Runbooks in Aviator execute through a carefully orchestrated step-by-step process that ensures reliable automation while providing visibility into each stage of your workflow. Understanding how this execution model works helps you design more effective Runbooks and troubleshoot issues when they arise.

### Sequential step processing

Every Runbook creates [one or more steps](../concepts/runbook-format.md), with larger tasks that may go into hundreds of steps and substeps. These steps are designed to be executed in-order as a subsequent step in the Runbook may have dependency on a previous step completed.

By default, runbook steps execute sequentially, with each step waiting for the previous one to complete before beginning. This sequential approach provides predictable behavior and makes it easier to understand the workflow logic, especially when steps have dependencies on outputs from earlier stages.

### Executing a single step

To execute a single step, simply click on "Execute Next" button on the steps view.

<figure><img src="../../.gitbook/assets/Screenshot 2025-10-28 at 4.41.17 PM.png" alt=""><figcaption></figcaption></figure>

You can alternatively also click on "Run" button next to a Step or a Substep. Keep in mind when clicking "Run" button that:

* when running the next substep, only that substep is executed,
* when running the parent step, it will enqueue and execute all substeps within that step one by one.

<figure><img src="../../.gitbook/assets/Screenshot 2025-10-28 at 4.41.26 PM.png" alt=""><figcaption></figcaption></figure>

### Step queuing

While a step is running, you can still enqueue more steps. This can be done by clicking on the "+" sign next to the steps. These steps will not immediately run and will be executed when the previous steps have been completed.

When enqueuing or running a step further down the order, Runbooks will automatically enqueue all steps in the middle that are not executed yet.

<figure><img src="../../.gitbook/assets/Screenshot 2025-10-28 at 5.14.37 PM.png" alt=""><figcaption></figcaption></figure>

### Stopping execution

Execution of any step can be perfomed by either clicking "Stop" on the Claude code message bar, or by clicking "Stop execution" button at the top right on the Steps view.

<figure><img src="../../.gitbook/assets/Screenshot 2025-10-28 at 5.18.02 PM.png" alt=""><figcaption></figcaption></figure>

In both cases, the execution is stopped and the step is marked as failed. Any subsequent steps that were queued will be automatically dequeued.

<figure><img src="../../.gitbook/assets/Screenshot 2025-10-28 at 5.17.57 PM.png" alt=""><figcaption></figcaption></figure>

***
