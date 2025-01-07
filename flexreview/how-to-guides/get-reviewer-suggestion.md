# Get a reviewer suggestion

With the Aviator app installed on your GitHub repository, it is possible to request suggested reviewers from FlexReview. This is done using a GitHub comment slash command:

```
/aviator flexreview suggest
```

FlexReview will post a comment showing the recommended reviewers, the PR author's expertise score, and alternate reviewers.

<figure><img src="../../.gitbook/assets/Screenshot 2025-01-06 at 3.55.49 PM.png" alt=""><figcaption></figcaption></figure>

## Understanding the results

### Reviewers suggested

This is the list of reviewers that would be assigned if the FlexReview Team was enabled.

### Owners

The list of [Direct owners](../concepts/recursive-ownership.md) for the code paths associated with the pull request.

### Common owners

The list of [Indirect owners](../concepts/recursive-ownership.md) for hte code paths associated with the pull request.

### Expert and load balance assignment result

The results represents the list of candidates (up to 5) that were considered for the code review assignment, along with their corresponding scores across all files, and their active review load. The final scores are then calculated by discounting expert scores based on the review load.

For instance, in the example above, the user `draftcode` has an expert score of `1.0`, but after discounting for the current load of 7, their score went down to `-0.4` .

## See Also

* [<mark style="color:blue;">Aviator Slash Commands</mark>](../../mergequeue/reference/slash-commands.md) For Aviator slash commands reference.
