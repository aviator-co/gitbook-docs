# Read-only mode

FlexReview's read-only mode allows you to test out FlexReview without interfering with any workflows.

## How it works

When you install the Aviator app and enable FlexReview, it starts in **read-only mode**, that is, it won’t take any actions or post any comments on the PRs automatically.

When in read-only mode, you can use [<mark style="color:blue;">Slash commands</mark>](../reference/flexreview-slash-commands.md) to get suggestions and status checks, or use the web dashboard for the same. FlexReview will not publish any comments automatically.

## Turning off read-only mode

To turn off read-only mode, activate FlexReview for one or more teams. You can do that directly from the [Teams dashboard](https://app.aviator.co/teams/last_viewed) by selecting the team in the dropdown.

To turn on read-only mode, to the [<mark style="color:blue;">teams list view</mark>](https://app.aviator.co/teams) and disable the FlexReview for all of them.

## Partially read-only

When FlexReview is enabled for a team, the code reviews that are owned by a specific team based on CODEOWNERS will receive suggestions and review assignments. If there is a code change that encompasses more than one team, the reviews will be assigned only for the teams that have FlexReview enabled.

## Global Enabled state vs Read-only mode

Along with team based enable / disable setting, FlexReview also has a global repository level setting. This global setting enables Aviator to index your past pull request data and GitHub teams data. When onboarding, you will enable global FlexReview to start indexing the data but it will still be in read-only mode. This state allows teams to test out the suggestions and status checks.

## Disabling FlexReview

If you disable FlexReview, it will stop indexing the GitHub pull request data in the background. When it is disabled, the Slash commands for FlexReview will also stop working.
