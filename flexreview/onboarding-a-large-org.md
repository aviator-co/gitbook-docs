---
description: >-
  Get instructions on onboarding a large org. Migrating from the strict controls
  of the Codeowners concept can enhance maintenance and bring other benefits.
---

# Onboarding a large org

Moving away from the strict Codeowners concept is a big cultural change. This requires carefully planning and testing the FlexReview approach. If you do not use `CODEOWNERS` requirements in your GitHub repository, you can skip this doc.

### Why migrate

Migrating away from the strict controls of the Codeowners concept can bring several benefits to your team. By adopting FlexReview, you can automate the assignment of reviewers based on their expertise, rather than defining very fine-grained access control. That means:

* No need to maintain a fine-grained file with hundreds of lines declaring ownership
* Ensure that the most qualified individuals provide feedback on each pull request
* Drastically reduce the number of reviewers required per code change
* Improve code review response times significantly

### Using Owners File

Aviator FlexReview introduces a an OWNERS file that helps with making code review assignments smarter without dropping the ownership for specific teams. The OWNERS file can be defined at`.aviator/OWNERS`. This file uses the similar format as the GitHub equivalent, but with the following limit:

* You can put only one team per line.
* You cannot have a negation rule.

The file is interpreted differently. Given a file path, owners are defined as follows:

* **Owners**: All matching teams are considered as an owner of the file.
* **Direct Owners:** The owner with the longest matching path is called a direct owner.
* **Indirect Owners:** Non-direct owners are called indirect owners.

When onboarding a large team, we recommend copying your current CODEOWNERS file as is to OWNERS, and then slowly simplifying your CODEOWNERS file to reduce the amount of gates needed.

To learn more about best practices for specifying ownership, also read [direct and indirect ownership](concepts/recursive-ownership.md).

### Starting with one team

To ease the transition and familiarize your team with FlexReview, we recommend starting with one specific team in your engineering org. By selecting a single team, you can test and evaluate the functionality of FlexReview in a controlled environment before expanding its usage to other teams. This approach allows you to gradually adapt to the new review process while minimizing any potential disruptions to your existing workflows.

#### Read-only mode

When you install the Aviator app for FlexReview, it starts in [read-only mode](concepts/read-only-mode.md), that is, it won’t take any actions or post any comments on the PRs automatically.

At this stage, you can use the slash command on any PR to get a suggestion from FlexReview:

```markdown
/aviator flexreview suggest
```

This will [suggest reviewers](how-to-guides/get-reviewer-suggestion.md) and post a message with a list of suggested reviewers. This will display the list of files and corresponding need for experts based along with suggested reviewers. It will suggest reviewers that also satisfy the Codeowners requirement.

#### Choosing the right team

You can [specify one or more teams](how-to-guides/whitelist-teams-for-reviewer-assignment.md) that you would like FlexReview to find reviewers for. You would ideally want to choose a team that gets a lot of review requests from outside of that team. That’s because these might be the reviews that hits the most bottlenecks and would benefit the most.

When specified, all PRs that touch the file paths owned by these teams will get a suggested reviewers GitHub comment from FlexReview. Since these directories are managed by Codeowners, FlexReview will only suggest valid reviewers based on the Codeowners file.

This would be a good opportunity to start educating the team about how FlexReview works and how to use it.&#x20;

#### Expanding code ownership by one-level

To get the real benefit of FlexReview, you should edit the `CODEOWNERS` file and expand the ownership of the sub-directory to the wider engineering org. You can start by expanding it by one-level.

<figure><img src="../.gitbook/assets/engineering-teams (2).png" alt=""><figcaption><p>Expanding ownership in engineering teams</p></figcaption></figure>

Let’s say a sub-directory has a team ownership of `ios-auth-eng` that manages the authentication framework for iOS, and there is a GitHub team `ios-eng` and a wider `eng` team. Here, you can replace that ownership of this sub-directory in the codeowners file to: `ios-eng` so that anyone on the ios engineering team can approve this change (from a `CODEOWNERS` perspective).

```markdown
ios/authentication/ @ios-eng
```

At this stage, FlexReview will start handling approval requirement, and introducing additional experts in the mix depending on code change complexity. Additionally, FlexReview will automatically start validating the complex reviews with the expert reviewers.

#### Expanding code ownership beyond

Once you have tested this with a few PRs, you can expand this further, and edit the `CODEOWNERS` file to all of `eng` for this sub-directory and continue testing it, while maintaining the `.aviator/OWNERS`file to define fine grain ownership.

If you have chosen a good team / project to start with, it’s likely that the developers are now familiar with how FlexReview works and comfortable with the idea of flexible reviews and expert reviewers. You can continue expanding the same way for additional sub-directories or onboarding everyone together.

### Compliance

Not all code paths are the same. As you are relaxing the requirements for code review, you can still require some security sensitive code paths to have strict ownership. As a rule of thumb, this should not represent more than 10% of your code base. As a cultural shift, you can internally define a process for teams to submit a request with clear reasoning that certain code paths should be gated. Based on that information, you can make a judgement call on where strict ownership is still needed.

## See also

* [Whitelist teams for reviewer assignment](how-to-guides/whitelist-teams-for-reviewer-assignment.md)
* [Troubleshooting reviewer assignment](how-to-guides/troubleshooting-reviewer-assignment.md)
* [Get a reviewer suggestion](how-to-guides/get-reviewer-suggestion.md)
* [Exclusing reviewers](how-to-guides/excluding-reviewers.md)

