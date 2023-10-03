# API Reference 

## **Authentication**

Aviator uses basic key based authentication. You can view and manage your API key in the [<mark style="color:blue;">Aviator dashboard</mark>](https://app.aviator.co/hooks/api). We only support a single API token. Once a token is generated, you can reset it to invalidate your existing token and create a new one.

API keys that are used to fetch Aviator analytics are currently read-only, but in the future these may be used for other purposes. Please make sure to keep them secure.

Authentication to the API is performed via [<mark style="color:blue;">HTTP Basic Auth</mark>](http://en.wikipedia.org/wiki/Basic\_access\_authentication)<mark style="color:blue;">.</mark> Provide your API key as the bearer token:

```shell
curl -H "Authorization: Bearer <aviator_token>"
-H "Content-Type: application/json"
https://api.aviator.co/api/v1/...
```

## GraphQL API (Beta)

&#x20;The GraphQL API (which is currently in beta) can be accessed at `https://api.aviator.co/graphql`. Authorization is required as described above.

An [interactive GraphQL playground](https://app.aviator.co/graphql) is also available which uses your current login credentials.

The GraphQL API is documented as part of the GraphQL schema. For more information on GraphQL, see the [official GraphQL guide](https://graphql.org/learn/queries/).

## REST API

### Repository

{% swagger method="post" path="/repo" baseUrl="https://api.aviator.co/api/v1" summary="Pause / unpause the merging of PRs on a repository." %}
{% swagger-description %}
Example:

`curl -X POST -H "Authorization: Bearer <aviator_token>"`\
`-H "Content-Type: application/json"`\
`-d '{"org": "org_name", "name": "repo_name", "paused": true }'`\
`https://api.aviator.co/api/v1/repo/`
{% endswagger-description %}

{% swagger-parameter in="body" name="org" type="String" required="true" %}
Name of the GitHub organization.
{% endswagger-parameter %}

{% swagger-parameter in="body" name="name" type="String" required="true" %}
Name of the repository in the GitHub organization.
{% endswagger-parameter %}

{% swagger-parameter in="body" name="paused" type="Boolean" required="true" %}
Whether to pause or unpause the queue
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Success" %}
```javascript
{
  "org": "ankitjaindce",
  "name": "testrepo",
  "paused": false
}
```
{% endswagger-response %}
{% endswagger %}

### Branches

{% swagger method="post" path="/branches" baseUrl="https://api.aviator.co/api/v1" summary="Pause / unpause the merging of PRs for specific base branches." %}
{% swagger-description %}
You can specify a glob pattern of base branches to pause or activate the Aviator queue. This ensures that you can continue merging branches to other base branches. You can override to pause / unpause all branches by using the Repository endpoint described above.

Example:

`curl -X POST -H "Authorization: Bearer <aviator_token>"`\
`-H "Content-Type: application/json"`\
`-d '{ "pattern": "release-*", "repository": {"org": "aviator", "name": "`av-demo-release`"}, "paused": true, "paused_message": "This release branch has been paused."}'`\
`https://api.aviator.co/api/v1/branches`
{% endswagger-description %}

{% swagger-parameter in="body" name="repository" type="Object" required="true" %}
Repository object associated with the branch
{% endswagger-parameter %}

{% swagger-parameter in="body" name="> org" type="String" required="true" %}
Organization associated with the repository
{% endswagger-parameter %}

{% swagger-parameter in="body" name="> name" type="String" required="true" %}
Name of the repository
{% endswagger-parameter %}

{% swagger-parameter in="body" name="pattern" type="String" required="true" %}
Glob pattern representing the base branch. E.g. `master` or `release-*`
{% endswagger-parameter %}

{% swagger-parameter in="body" name="paused" type="Boolean" required="true" %}
Whether to pause or unpause the queue
{% endswagger-parameter %}

{% swagger-parameter in="body" name="paused_message" type="String" %}
A customized message to post on the top PR when this branch is paused.
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="" %}
```json
{
    "pattern": "release-*",
    "repository": {
      "org": "aviator",
      "name": "av-demo-release"
    },
    "paused": true
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger method="get" path="/branches" baseUrl="https://api.aviator.co/api/v1" summary="Get base branches and their statuses (paused / unpaused)" %}
{% swagger-description %}
You can specify a glob pattern of base branches to fetch the status of. If not provided, it will fetch the status of all base branches for a specific repository.\
\
Example:

`curl -H "Authorization: Bearer <aviator_token>"`\
`-H "Content-Type: application/json"`\
`-d '{ "repository": {"org": "aviator", "name": "`av-demo-release`"}, "pattern": "release-*"}'`\
`https://api.aviator.co/api/v1/branches`
{% endswagger-description %}

{% swagger-parameter in="query" name="repository" type="Object" required="true" %}
Repository object associated with the branch
{% endswagger-parameter %}

{% swagger-parameter in="query" name="> org " type="String" required="true" %}
Organization associated with the repository
{% endswagger-parameter %}

{% swagger-parameter in="query" name="> name" type="String" required="true" %}
Name of the repository
{% endswagger-parameter %}

{% swagger-parameter in="query" name="pattern" type="String" %}
Glob pattern representing the base branch. E.g. `master` or `release-*`
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Success" %}
```json
{
    "branches": [
        {
            "pattern": "release-*",
            "paused": true
            "paused_message": "This branch is currently paused for the release"
        }
    ],
    "repository": {
        "name": "av-demo-release"
        "org": "aviator"
    }
}
```
{% endswagger-response %}
{% endswagger %}

### PullRequest

{% swagger method="post" path="/pull_request" baseUrl="https://api.aviator.co/api/v1" summary="Queue or Dequeue a Pull Request" %}
{% swagger-description %}
`curl -X POST -H "Authorization: Bearer <aviator_token>"`\
`-H "Content-Type: application/json"`\
`-d '{"action": "queue", "pull_request": {"number": 1234, "repository": {"name": "repo_name", "org": "org_name"}, "head_commit_sha":" "`69f4404fda48aa2932abfbcb6956a9ccd473b17d`", "affected_targets": ["targetA", "targetB"], "merge_commit_message": {"title": "This is where title goes", "body": "This is where body goes"}}}'`\
`https://api.aviator.co/api/v1/pull_request/`
{% endswagger-description %}

{% swagger-parameter in="body" name="action" required="true" %}
Action taken. Valid options: `update`, `queue` or `dequeue`
{% endswagger-parameter %}

{% swagger-parameter in="body" name="pull_request" type="Object" required="true" %}
PullRequest object representing the PR that is queued.
{% endswagger-parameter %}

{% swagger-parameter in="body" name="> number" required="true" %}

{% endswagger-parameter %}

{% swagger-parameter in="body" name="> repository" type="Object" required="true" %}
Repository object associated with the PR
{% endswagger-parameter %}

{% swagger-parameter in="body" name="> > name" required="true" %}
Name of the repository
{% endswagger-parameter %}

{% swagger-parameter in="body" name="> > org" required="true" %}
Organization associated with the repository
{% endswagger-parameter %}

{% swagger-parameter in="body" name="> head_commit_sha" %}
Representing the commit SHA of the head of the PR.
{% endswagger-parameter %}

{% swagger-parameter in="body" name="> affected_targets" type="List[String]" %}
Affected targets for the PR. Please see [Affected Targets](mergequeue/affected-targets/) section for more details.
{% endswagger-parameter %}

{% swagger-parameter in="body" name="> merge_commit_message" type="Object" %}
CommitMessage object
{% endswagger-parameter %}

{% swagger-parameter in="body" name="> > title" %}
Title of merge commit message
{% endswagger-parameter %}

{% swagger-parameter in="body" name="> > body" %}
Body of merge commit message
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="" %}
```javascript
{
  "pull_request": {
    "number": 1234,
    "status": "queued",
    "queued": true,
    "title": "[TASK-123] This is a new bug",
    "author": "av_user",
    "head_branch": "av/new_fix",
    "base_branch": "main",
    "repository": {
      "name": "av-demo",
      "org": "aviator-co"
    }
  }
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger method="post" path="/pull_request/backport" baseUrl="https://api.aviator.co/api/v1" summary="Request to backport a PR on the specified target branch." %}
{% swagger-description %}
Example:

`curl -X POST -H "Authorization: Bearer <aviator_token>"`\
`-H "Content-Type: application/json"`\
`-d '{"target_branch": "release-v1", "`source\_pull`": {"number": 1234, "repository": {"name": "repo_name", "org": "org_name"}}}'`\
`https://api.aviator.co/api/v1/pull_request/backport`


{% endswagger-description %}

{% swagger-parameter in="body" name="target_branch" type="String" required="true" %}
Name of the base branch to backport this PR to
{% endswagger-parameter %}

{% swagger-parameter in="body" name="source_pull" type="Object" required="true" %}
PullRequest object for the current branch
{% endswagger-parameter %}

{% swagger-parameter in="body" name="> number" type="Integer" required="true" %}
PullRequest number
{% endswagger-parameter %}

{% swagger-parameter in="body" name="> repository" type="Object" required="true" %}
GitHub repository associated with the PullRequest
{% endswagger-parameter %}

{% swagger-parameter in="body" name="> > org" type="String" required="true" %}
Organization associated with the repository
{% endswagger-parameter %}

{% swagger-parameter in="body" name="> > name" type="String" %}
Name of the repository
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="" %}
```json
{
    "source_pull": {
      "number": 1234,
      "repository": {
        "org": "aviator",
        "name": "av-demo-release"
      }
    },
    "target_branch": "release-v1",
    "message": "Backporting initiated for 1234 to release-v1. Check comments in the PR #1234 for the status"
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger method="get" path="/pull_request" baseUrl="https://api.aviator.co/api/v1" summary="Fetch information of a PR based on the branch name or number" %}
{% swagger-description %}
Example:

`curl -H "Authorization: Bearer <aviator_token>"`\
`-H "Content-Type: application/json"`\
`https://api.aviator.co/api/v1/pull_request?org=orgname&repo=reponame&branch=branchname`&#x20;
{% endswagger-description %}

{% swagger-parameter in="query" name="org" type="String" required="true" %}
Organization associated with the repository
{% endswagger-parameter %}

{% swagger-parameter in="query" name="repo" type="String" required="true" %}
Name of the repository
{% endswagger-parameter %}

{% swagger-parameter in="query" name="branch" type="String" required="true" %}
Feature branch associated with PR. One of `branch` or `number` must be present.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="number" type="Integer" required="true" %}
PR number to fetch. One of `branch` or `number` must be present.
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Success" %}
```javascript
{
    "author": "author-name",
    "base_branch": "master",
    "head_branch": "mq-qa-8440-1",
    "number": 89,
    "queued": true,
    "queued_at": "2022-11-16T17:21:41.350499",
    "repository": {
        "name": "repo-name",
        "org": "org-name"
    },
    "skip_line": false,
    "status": "queued",
    "title": "mq-qa-8440-1"
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger method="get" path="pull_request/queued" baseUrl="https://api.aviator.co/api/v1/" summary="Fetch information of PRs that are in the queued state" %}
{% swagger-description %}
Example:&#x20;

`curl -H "Authorization: Bearer <aviator_token>"`\
`-H "Content-Type: application/json"`\
`https://api.aviator.co/api/v1/pull_request/queued?org=orgname&repo=reponame&base_branch=master`&#x20;


{% endswagger-description %}

{% swagger-parameter in="query" name="org" type="String" required="true" %}
Organization associated with the repository
{% endswagger-parameter %}

{% swagger-parameter in="query" name="repo" type="String" required="true" %}
Name of the repository
{% endswagger-parameter %}

{% swagger-parameter in="query" name="base_branch" type="String" %}
Target branch to fetch queued PRs for
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Success" %}
```javascript
{
    "pull_requests": [
        {
            "author": "ohcnivek",
            "base_branch": "master",
            "head_branch": "mq-qa-8440-1",
            "number": 89,
            "queued": true,
            "queued_at": "2022-11-16T17:21:41.350499",
            "repository": {
                "name": "kevin-test",
                "org": "ohcnivek"
            },
            "skip_line": false,
            "status": "queued",
            "title": "mq-qa-8440-1"
        },
        {
            "author": "ohcnivek",
            "base_branch": "master",
            "head_branch": "mq-qa-8440-2",
            "number": 90,
            "queued": true,
            "queued_at": "2022-11-16T17:21:50.001884",
            "repository": {
                "name": "kevin-test",
                "org": "ohcnivek"
            },
            "skip_line": false,
            "status": "queued",
            "title": "mq-qa-8440-2"
        },
        {
            "author": "ohcnivek",
            "base_branch": "master",
            "head_branch": "mq-qa-8440-3",
            "number": 91,
            "queued": true,
            "queued_at": "2022-11-16T17:22:00.185823",
            "repository": {
                "name": "kevin-test",
                "org": "ohcnivek"
            },
            "skip_line": false,
            "status": "queued",
            "title": "mq-qa-8440-3"
        }
    ]
}
```
{% endswagger-response %}
{% endswagger %}

### BotPullRequest

{% swagger method="get" path="/bot_pull_request" baseUrl="https://api.aviator.co/api/v1" summary="Fetch information of PRs associated with a provided Bot PullRequest" %}
{% swagger-description %}
A Bot PR is a draft PR created during parallel mode to validate the CI.

Example:&#x20;

`curl -H "Authorization: Bearer <aviator_token>"`\
`-H "Content-Type: application/json"`\
`https://api.aviator.co/api/v1/bot_pull_request?org=orgname&repo=reponame&number=1234`&#x20;
{% endswagger-description %}

{% swagger-parameter in="query" name="org" type="String" required="true" %}
Organization associated with the repository
{% endswagger-parameter %}

{% swagger-parameter in="query" name="repo" type="String" required="true" %}
Name of the repository
{% endswagger-parameter %}

{% swagger-parameter in="query" name="number" type="Integer" required="true" %}
PR number associated with the Bot PR
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Success" %}
```
{
  "head_commit_sha": "acffa575c02f2cf000aabe69f44e8905bc04c25d",
  "pull_requests": [
    {
      "author": "author-name",
      "base_branch": "master",
      "head_branch": "mq-qa-8440-1",
      "number": 89,
      "queued": true,
      "queued_at": "2022-11-16T17:21:41.350499",
      "repository": {
          "name": "repo-name",
          "org": "org-name"
      },
      "skip_line": false,
      "status": "queued",
      "title": "mq-qa-8440-1"
    }
  ]
}
```
{% endswagger-response %}
{% endswagger %}

### Config

{% swagger method="get" path="/config" baseUrl="https://api.aviator.co/api/v1" summary="Fetch the current YAML config associated with the given GitHub repository." %}
{% swagger-description %}
Example:

`curl -H "Authorization: Bearer <aviator_token>"`\
`-H "Content-Type: application/json"`\
`https://api.aviator.co/api/v1/config?org=orgname&repo=reponame`
{% endswagger-description %}

{% swagger-parameter in="query" name="org" required="true" %}
Organization associated with the GitHub repository.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="repo" required="true" %}
Name of the GitHub repository.
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="" %}
```
merge_rules:
   labels:
     trigger: "mergequeue"
   merge_mode:
    type: "parallel"
    parallel_mode:
      max_parallel_builds: 10
      override_required_checks:
        - build_and_test
        - 'ci/circleci: build'
```
{% endswagger-response %}
{% endswagger %}

###

{% swagger method="post" path="/config" baseUrl="https://api.aviator.co/api/v1" summary="Change the YAML config associated with the given GitHub repository." %}
{% swagger-description %}
The request accepts the payload as the raw string YAML format, and returns back response in a JSON format.
{% endswagger-description %}

{% swagger-parameter in="query" name="org" required="true" %}
Organization associated with a GitHub repository
{% endswagger-parameter %}

{% swagger-parameter in="query" name="repo" required="true" %}
Name of the GitHub repository.
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="" %}
```
{
  "success": true
}
```
{% endswagger-response %}

{% swagger-response status="400: Bad Request" description="" %}
```
{
    "errors": [
        "mergeRules -> mergeMode -> parallelMode -> updateBeforeRequeue: value could not be parsed to a boolean",
        "mergeRules -> mergeMode -> parallelMode -> checkMergeabilityToQueue: value could not be parsed to a boolean"
    ],
    "success": false
}
```
{% endswagger-response %}
{% endswagger %}

### Config Change

{% swagger method="get" path="/config/history" baseUrl="https://api.aviator.co/api/v1" summary="Fetch the history of config changes associated with a given repository." %}
{% swagger-description %}
Returns a list of config history events as diffs of changes. `repo` and `org` must be provided.

Example:

`curl -H "Authorization: Bearer <aviator_token>"`\
`-H "Content-Type: application/json"`\
`https://api.aviator.co/api/v1/config/history?org=orgname&repo=reponame`
{% endswagger-description %}

{% swagger-parameter in="query" name="org" type="String" required="true" %}
Organization associated with the repository
{% endswagger-parameter %}

{% swagger-parameter in="query" name="repo" type="String" required="true" %}
Name of the repository
{% endswagger-parameter %}

{% swagger-parameter in="query" name="start" type="String" %}
UTC Start date in _YYYY-MM-DD_ format. Example: 2021-07-21
{% endswagger-parameter %}

{% swagger-parameter in="query" name="end" type="String" %}
UTC End date in _YYYY-MM-DD_ format. Example: 2021-07-21
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Success" %}
```
{
  "repository": {
    "name": "mergeit",
    "org": "aviator"
  },
  "history": [{
      "applied_by": {
        "email": "email@email.com",
        "gh_username": "jainankit"
      },
      "applied_at": "2022-11-16T17:21:41.350499Z"
      "commit_sha": "85d419bbca585f04456083fd98b7858c0f1e4d13",
      "diff": "-     publish: \"always\"\n+     publish: \"ready\"",
    },
    {
      ..
    }
  ]
}
```
{% endswagger-response %}
{% endswagger %}

The `modified_by` property contains email and gh\_username. If the config was modified from the Dashboard, `email` of the user would be present, and if the config was modified from the GitHub repo change, a `gh_username` would be present. `commit_sha` property may also be only present if the change was made from the GitHub repository.

### Analytics

{% swagger method="get" path="/v1/analytics" baseUrl="https://api.aviator.co/api" summary="Get list of analytics objects representing statistics on a daily basis." %}
{% swagger-description %}
Example:

`curl -H "Authorization: Bearer <aviator_token>"`\
`-H "Content-Type: application/json"`\
`https://api.aviator.co/api/v1/analytics/?start=2021-07-14&end=2021-07-21&timezone=America/Los_Angeles&repo=orgname/reponame`
{% endswagger-description %}

{% swagger-parameter in="query" name="start" required="false" type="String" %}


UTC Start date in _YYYY-MM-DD_ format. Example: 2021-07-21
{% endswagger-parameter %}

{% swagger-parameter in="query" name="end" required="false" type="String" %}
UTC End date in _YYYY-MM-DD_ format. Example: 2021-07-21
{% endswagger-parameter %}

{% swagger-parameter in="query" name="repo" required="true" type="String" %}
Name of the GitHub repo, in the format: _orgname/reponame_
{% endswagger-parameter %}

{% swagger-parameter in="query" name="timezone" type="String" %}
Standard tz format string. Defaults to account timezone. Example: America/Los\_Angeles
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Success" %}
{% tabs %}
{% tab title="Parameters" %}
<table><thead><tr><th width="201.5919403422235">Parameter</th><th>Description</th></tr></thead><tbody><tr><td><strong>time_in_queue</strong></td><td>List of objects representing time spent by PRs in queue</td></tr><tr><td><strong>>date</strong></td><td>UTC End date in <em>YYYY-MM-DD</em> format. Example: 2021-07-14</td></tr><tr><td><strong>>min</strong></td><td>Minimum time in seconds</td></tr><tr><td><strong>>avg</strong></td><td>Average time in seconds</td></tr><tr><td><strong>>p50</strong></td><td>50th percentile in seconds</td></tr><tr><td><strong>>p75</strong></td><td>75th percentile in seconds</td></tr><tr><td><strong>>p90</strong></td><td>90th percentile in seconds</td></tr><tr><td><strong>>p100</strong></td><td>100th percentile in seconds</td></tr><tr><td><strong>mergequeue_usage</strong></td><td>List of objects representing the Aviator bot usage compared to total PRs merged.</td></tr><tr><td><strong>>date</strong></td><td>UTC End date in <em>YYYY-MM-DD</em> format. Example: 2021-07-14</td></tr><tr><td><strong>>total</strong></td><td>Total number of PRs merged</td></tr><tr><td><strong>>merged_by_bot</strong></td><td>Total number of PRs merged by Aviator bot</td></tr><tr><td><strong>blocked_reason</strong></td><td>List of objects representing the blocked reasons identified by the Aviator bot while processing queued PRs.</td></tr><tr><td><strong>>date</strong></td><td>UTC End date in <em>YYYY-MM-DD</em> format. Example: 2021-07-14</td></tr><tr><td><strong>>merge_conflict</strong></td><td>Failed due to merge conflict</td></tr><tr><td><strong>>ci_failure</strong></td><td>Failed due to CI status check failure. This only accounts for required check failures.</td></tr><tr><td><strong>>manual_dequeue</strong></td><td>A PR was manually removed from the queue</td></tr><tr><td><strong>>ci_timeout</strong></td><td>CI timed out based on the configuration in MergeQueue rules</td></tr><tr><td><strong>>other</strong></td><td>Failed due to any other reason</td></tr><tr><td><strong>sync_frequency</strong></td><td>List of objects representing how many times a PR fetched a base branch on an aggregate basis.</td></tr><tr><td><strong>>date</strong></td><td>UTC End date in <em>YYYY-MM-DD</em> format. Example: 2021-07-14</td></tr><tr><td><strong>>min</strong></td><td>Minimum sync times</td></tr><tr><td><strong>>avg</strong></td><td>Average sync times</td></tr><tr><td><strong>>p50</strong></td><td>50th percentile of number of sync times</td></tr><tr><td><strong>>p75</strong></td><td>75th percentile of number of sync times</td></tr><tr><td><strong>>p90</strong></td><td>90th percentile of number of sync times</td></tr><tr><td><strong>>p100</strong></td><td>100th percentile of number of sync times</td></tr></tbody></table>
{% endtab %}

{% tab title="Example" %}
```javascript
{
    "time_in_queue" : [
      {
        "date": "2021-07-14",
        "min": 12,
        "avg": 24,
        "p50": 23,
        "p75": 28,
        "p90": 32,
        "p100": 40
      },
      {
        "date": "2021-07-15",
        "min": 12,
        "avg": 24,
        "p50": 23,
        "p75": 28,
        "p90": 32,
        "p100": 40
      },
      {...},
      {...}
    ],
    "mergequeue_usage": [
      {
        "date": "2021-07-14",
        "total": 52,
        "merged_by_bot": 40
      },
      {
        "date": "2021-07-15",
        "total": 52,
        "merged_by_bot": 40
      },
      {...},
      {...}
    ],
    "blocked_reason": [
      {
        "date": "2021-07-14",
        "merge_conflict": 2,
        "ci_failure": 4,
        "manual_dequeue": 6,
        "ci_timeout": 1,
        "other": 0
      },
      {
        "date": "2021-07-15",
        "merge_conflict": 2,
        "ci_failure": 4,
        "manual_dequeue": 6,
        "ci_timeout": 1,
        "other": 0
      },
      {...},
      {...}
    ],
    "sync_frequency": [
      {
        "date": "2021-07-14",
        "min": 1,
        "avg": 1,
       "p50": 2.34,
        "p75": 2.6,
        "p90": 3.2,
        "p100": 4
      },
      {
        "date": "2021-07-15",
        "min": 1,
        "avg": 1.2,
        "p50": 2.34,
        "p75": 2.6,
        "p90": 3.2,
        "p100": 4
      },
      {...},
      {...}
    ]
  }
```
{% endtab %}
{% endtabs %}
{% endswagger-response %}
{% endswagger %}

### Queue

{% swagger method="get" path="/v1/queue/stats" baseUrl="https://api.aviator.co/api" summary="Get live statistics about the state of the merge queue" %}
{% swagger-description %}
Currently this endpoint only reports statistics about the depth of the queue.
{% endswagger-description %}

{% swagger-parameter in="query" name="org" type="String" required="true" %}
The GitHub organization that the repo belongs to.
{% endswagger-parameter %}

{% swagger-parameter in="query" name="repo" type="String" required="true" %}
The name of the GitHub repo.
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="Success" %}
```javascript
{
  // Stats about the current depth of the queue.
  "depth": {
    // The total number of PRs that are in the queue.
    // Excludes PRs that have not been queued yet or that have been
    // marked as blocked.
    "queued": 8, 
    // The number of PRs actively being processed.
    // In serial mode, this value is always equal to the "queued" value.
    // In parallel mode, this will be at most the "max_parallel_builds" setting
    // and indicates the numbers of PRs that have draft PRs created.
    "processing": 2, 
    // The number of PRs that are queued but not yet being processed.
    // In parallel mode, this is equal to queued - processing.
    // In serial mode, this is always zero.
    "waiting": 6
  }
}
```
{% endswagger-response %}
{% endswagger %}
