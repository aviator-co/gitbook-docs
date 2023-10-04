# Instant Merges

Instant merge is a merge method where Aviator merges the PR directly without waiting for the CI to finish, or in a parallel mode without creating a draft PR for full CI validation. This can be configured using the Pilot workflow.

{% hint style="info" %}
To merge PR without CI completion, you will require [<mark style="color:blue;">elevated permissions</mark>](instant-merges.md#elevated-permissions) for Aviator.
{% endhint %}

### Why use instant merge

It can sometimes be important to get the change out without waiting for an extra CI cycle. For instance, maybe there’s a production outage, or some wrong configuration was deployed in production and has to be updated.

### How to configure

To configure Instant merge using Pilot configs, you can define a scenario with a trigger label:

```
scenarios:
- name: "instant merge"
  trigger:
    pull_request:
      labeled:
        label "av-instant-merge"
  actions:
  - mergequeue:
      instant_merge: {}
```

We also recommend restricting instant merge capability to select few users. Aviator’s Pilot framework also supports restricting who applied a label. You can either provide a GitHub username or a team using `@org/team`.

```
scenarios:
- name: "instant merge"
  trigger:
    pull_request:
      labeled:
        label "av-instant-merge"
        labeled_by: “@aviator-co/leads”
  actions:
  - mergequeue:
      instant_merge: {}
```

### Elevated permissions

If you only need instant merge to merge PRs while bypassing creating a draft PR in parallel mode, you do not need to provide any elevated permissions.

GitHub does not allow thirdparty bots to bypass branch protection when using the classic branch protection rules.

#### Using Rulesets (recommended)

This capability is now available using GitHub’s [<mark style="color:blue;">Rulesets</mark>](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/managing-rulesets-for-a-repository). Rulesets offer feature parity with the configurations you use in classic branch protection rules and you should be able to migrate all your existing configurations to using Rulesets.

Once migrated to Ruleset, you can add Aviator app in the bypass list.

<figure><img src="../../.gitbook/assets/Screen Shot 2023-05-26 at 11.26.05 AM.png" alt=""><figcaption><p>add Aviator app in the bypass list for the Ruleset</p></figcaption></figure>

#### Using personal access token

If you cannot migrate your branch protection rules to Ruleset, you can also use a Personal Access Token associated with a user who has GitHub admin permissions. Remember to give repo: read-write permissions when generating a token. In this case, when using instant merge, Aviator will use this personal access token.

If you need assistance in setting up the personal access token, please contact [<mark style="color:blue;">howto@aviator.co</mark>](mailto:howto@aviator.co).

\
\
\
