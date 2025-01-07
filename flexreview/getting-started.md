# Getting started

FlexReview is a low-config tool that uses pull request history to dynamically understand code ownership.

### Step 1: Connect

During the initial setup, you will be asked to connect the Aviator GitHub app to your GitHub repository to use FlexReview.&#x20;

<figure><img src="../.gitbook/assets/Screenshot 2024-02-09 at 12.08.18 PM.png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
If you have trouble connecting the app, please read the [<mark style="color:blue;">troubleshooting doc</mark>](../manage/faqs/troubleshooting-github-app-connection.md).
{% endhint %}

### Step 2: Activate

At this point, you can choose the repository you want to start with. When activating FlexReview on a GitHub repository, it'll be enabled in the [<mark style="color:blue;">**read-only mode**</mark>](broken-reference). That means, FlexReview won't interact with any PRs directly, but you will be able to use the slash commands. This gives you an opportunity to fine-tune and test out FlexReview before turning on the automation.

<figure><img src="../.gitbook/assets/Screenshot 2024-02-09 at 12.11.36 PM.png" alt=""><figcaption></figcaption></figure>

### Step 3: Test suggestions

FlexReview make take a few seconds to index some teams and pull request data from your repository. Once indexed, you should be able to test out the suggestions. Remember that the suggestions here are still imperfect since FlexReview has only completed initial indexing. The full indexing continues in the background.\


Enter the pull request number in the textbox to see the suggestions on this page, or use the Slash command in GitHub comments to get suggestions directly in your pull request view.

<figure><img src="../.gitbook/assets/flexreview-comment.png" alt=""><figcaption><p>Slash command to get suggestions in the GitHub PR details page</p></figcaption></figure>

{% hint style="info" %}
If it takes longer than 60 seconds to index the initial data, please try refreshing the page, or contact us at **howto@aviator.co.**
{% endhint %}

### Step 4: Picking the Team

FlexReview requires a minimal configuration. Once you have tested Aviator FlexReview manually using Slash commands, you can start activating it for specific teams in your organization. Once activated, you can now go to the Team config and set up the reviewer assignment rules and optionally define the SLOs and the automated actions. All subsequent pull requests that modify the code owned by this team will start seeing reviewer suggestion comments and assign reviewers based on the configured policy.



To learn more about how FlexReview works, please read the [Concepts](concepts/) and [How-to-guides](../releases-beta/how-to-guides/) and check out the complete [reference guide](reference/) to understand all the configurations.

If you have any questions or comments, please reach out to **howto@aviator.co**
