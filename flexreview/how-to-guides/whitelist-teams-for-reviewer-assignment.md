# Whitelist Teams for reviewer assignment

When [onboarding a large org](../onboarding-a-large-org.md), it may be preferred to test out reviewer assignment for a select number of pull requests. There are a couple of ways to do selective reviewer assignment.

### Whitelist by reviewers

To enable FlexReview reviewer assignment for a given GitHub team, go to Teams section in Dashboard, and select the desired team in the dropdown. For this view, click "Enable FlexReview Team".

<figure><img src="../../.gitbook/assets/Screenshot 2025-01-07 at 4.10.32 PM.png" alt=""><figcaption><p>Enable FlexReview Team</p></figcaption></figure>

Once enabled, you should be able to navigate to the Team Config and ensure that "Expert Review" is selected for in the Review Assignment section.&#x20;

<figure><img src="../../.gitbook/assets/Screenshot 2025-01-07 at 4.11.58 PM.png" alt=""><figcaption><p>Review Assignment</p></figcaption></figure>

### Whitelist by authors

It may be desired to only assign reviews for the pull requests that are authored by specific users or specific teams. This can be configured at the repository level.&#x20;

Go to Repositories > FlexReview Status > Configure for the repository that you want to enable this for.

<figure><img src="../../.gitbook/assets/Screenshot 2025-01-07 at 3.29.56 PM.png" alt=""><figcaption><p>FlexReview Configure</p></figcaption></figure>

Go to "Author Allowlist". Here you can specify either a user or a team (one per-line) that would be whitelisted for reviewer assignments. Syntax is `@username` for users and `@org/team` for teams. By default all authors are part of the allowlist, this is represented by a special marker `*`.

<figure><img src="../../.gitbook/assets/Screenshot 2025-01-07 at 4.16.03 PM.png" alt=""><figcaption><p>Author Allowlist</p></figcaption></figure>

### Working with both

Please note that these whitelists act as AND-gates, that means a pull request will be assigned a reviewer when it satisfies bot the authors and the reviewers whitelists.

## See also

* [Onboarding a large org](../onboarding-a-large-org.md)
* [Troubleshooting reviewer assignment](troubleshooting-reviewer-assignment.md)
