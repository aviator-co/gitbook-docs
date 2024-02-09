# Get a reviewer suggestion



### Manual Slash command

With the aviator app installed on your GitHub repository, it is possible to request suggested reviewers from FlexReview. This is done using a github comment slashcommand:

```
/aviator flexreview suggest
```

Afterwards, we can see a comment posted by FlexReview showing the recommended reviewers, their calculated score for this PR and their review load. 


### View suggested reviewers without adding comments to a PR

The Aviator webapp provides a way to see FlexREview suggestions without writing a comment to your pull requests. Visit [<mark style="color:blue;">Aviator FlexReview Suggest</mark>](https://app.aviator.co/flexreview/suggest). 

Choose the respective repository.

![WebApp Repo Choice](<../../.gitbook/assets/top_of_revsug_dock.png>)

Then enter a pull request number to see suggested reviewers. 

![WebApp Suggested Reviewer](<../../.gitbook/assets/FR_webapp_example.png>)


### Automatic reviewer suggestion on PRs

Automatic reviewer suggestion can be configured in the Aviator web app. See the [<mark style="color:blue;">configuration page.</mark>](https://app.aviator.co/flexreview/config) With this enabled, Avaitor will post an autmatic FlexReview suggestion on to pull requests that contain files matching the file path prefix. 


![WebApp Sub Repo paths](<../../.gitbook/assets/FR_sub-repo-config.png>)

## See Also
* [<mark style="color:blue;">Aviator Slash Commands</mark>](../../mergequeue/reference/slash-commands.md) For aviator slash commands reference.
