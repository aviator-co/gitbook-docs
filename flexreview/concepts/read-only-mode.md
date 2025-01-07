# Read-only mode

FlexReview's read-only mode allows you to test out FlexReview without interfering with any workflows.

<figure><img src="../../.gitbook/assets/Screenshot 2024-02-10 at 2.15.49 PM.png" alt=""><figcaption><p>FlexReview in read-only mode</p></figcaption></figure>

## How it works

When you install the Aviator app and enable FlexReview, it starts in **read-only mode**, that is, it won’t take any actions or post any comments on the PRs automatically.

When in read-only mode, you can use [<mark style="color:blue;">Slash commands</mark>](../reference/flexreview-slash-commands.md) to get suggestions and status checks, or use the web dashboard for the same. FlexReview will not publish any comments automatically.

## Turning off read-only mode

To turn off read-only mode, specify the Sub-repo configuration on the [<mark style="color:blue;">FlexReview Config page</mark>](https://app.aviator.co/flexreview/config)<mark style="color:blue;">.</mark> If you want to enable reviewer suggestions for the entire repository, set the file path for Reviewer Suggestion to `*`.&#x20;

To turn on read-only mode, set the Sub-repo configuration for Reviewer Suggestion and Approval Check to empty.

## Partially read-only

You can also set the value of Reviewer Suggestion to a specific path that you want the suggestions to be enabled for. In that case, FlexReview will only post the reviewer suggestion comments for pull requests that modify at least one file matching that path. The same applies for the Approval Check property.

## Enabled state vs Read-only mode

After enabling FlexReview in your repository, it will index your past pull request data and GitHub teams data. At this point, even though FlexReview is enabled, it is still in read-only mode. This state allows teams to test out the suggestions and status checks.

## Disabling FlexReview

If you disable FlexReview, it will stop indexing the GitHub pull request data in the background. When it is disabled, the Slash commands for FlexReview will also stop working.
