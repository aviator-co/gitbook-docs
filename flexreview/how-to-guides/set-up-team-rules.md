# Set up team rules

Navigate to the FlexReview Teams page to start your setup.

## Team Dashboard

On the dashboard, admins can select their team from the dropdown and see SLO metrics. The dashboard also shows which specific PRs have failed to meet the defined SLO time.

<figure><img src="../../.gitbook/assets/Screen Shot 2024-04-24 at 5.27.36 PM.png" alt=""><figcaption><p>View SLO metrics via the dashboard.</p></figcaption></figure>

## Team Config

Navigate over to the `Team Config` to set up your team-specific rules.

### Review Assignment

Specify how you want reviews assigned on your team. Options include expert review, round robin, or no automatic assignment.

<figure><img src="../../.gitbook/assets/Screen Shot 2024-04-24 at 5.44.13 PM.png" alt="" width="563"><figcaption><p>Define review assignment.</p></figcaption></figure>

### SLO Config

You can set both internal and external review SLOs, and ensure that the SLO is only applied to PRs of a certain size. The internal SLO applies to reviewers on the same team, and the external SLO applies to members of other teams.

<figure><img src="../../.gitbook/assets/Screen Shot 2024-04-24 at 5.44.52 PM.png" alt="" width="563"><figcaption><p>Set up team-specific SLOs.</p></figcaption></figure>

### Policies for Automated Actions

FlexReview also allows teams to set up policies for automated actions. If multiple teams are responsible for a single PR, all rules for the relevant teams will be triggered.

Trigger options:

* When a new reviewer is assigned
* When there is no review activity for X hours

Action options:

* Send a Slack DM to the reviewer
* Send a Slack message to the team channel
* Reassign the PR to another reviewer

<figure><img src="../../.gitbook/assets/Screen Shot 2024-04-24 at 5.45.44 PM.png" alt="" width="563"><figcaption><p>Set up automated actions for you team.</p></figcaption></figure>
