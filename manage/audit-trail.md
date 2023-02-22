# Audit Trail

It is important to know when your merge rules have been changed, and who changed them. We store a history of your merge rules configuration so that you can see exactly that.

Audit trail information can be found on the [<mark style="color:blue;">Merge Rules</mark>](https://app.aviator.co/github/rules) page under the `Configuration History` tab.

## What Gets Stored?

For each of the ways that you may set up your merge rules, we store different relevant information. An overview of the differences can be found below.

### Dashboard UI

When changes are made from the [<mark style="color:blue;">Merge Rules</mark>](https://app.aviator.co/github/rules) dashboard, we store the logged in `aviator_user` that made them, the time the change was made at, and a `diff` of the new configuration to the previous one.

<figure><img src="../../.gitbook/assets/Screen Shot 2022-09-19 at 6.01.12 PM.png" alt=""><figcaption><p>A configuration history record of a change made via Dashboard UI</p></figcaption></figure>

As can be seen above, the `aviator_user` is displayed as the email associated with their Aviator account. The `diff` itself is a simple text difference between the current and previous YAML displayed on the `Yaml Configuration` tab.

### Dashboard YAML

Similarly to the above, when changes are made to [<mark style="color:blue;">Merge Rules</mark>](https://app.aviator.co/github/rules) YAML under the `Yaml Configuration` tab, we store the `aviator_user` that logged in to make the change, the time, and the `diff` of the current and previous YAML.

Since changes here are made directly to the YAML shown on the `Yaml Configuration` tab, it should be more obvious how the text difference is generated in this case.

The configuration history record for dashboard YAML changes looks exactly the same as changes from the dashboard UI, so we won't repeat the example image.

### Repository Config File

Finally, when your YAML configuration file is set up in your repo, we store the `github_user` who pushed the changes onto your repo's default branch, the commit the change was made in, the time of the commit, and again the `diff` between the updated config file and the previous one.

<figure><img src="../../.gitbook/assets/Screen Shot 2022-09-21 at 3.48.29 PM.png" alt=""><figcaption><p>A configuration history record of a change made to the repository config YAML file</p></figcaption></figure>

The `github_user` is displayed as their GitHub `login` or username. The commit is represented as the short hash which links to the actual commit information.

While under this configuration set up, your source control also can act as its own audit trail. However, these changes are additionally stored in our database both for completeness and to make it clear what changes actually made it through in the event of any desync between `Aviator` and your source control.
