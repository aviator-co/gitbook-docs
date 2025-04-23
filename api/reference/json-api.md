---
description: Access Aviator's JSON API reference documentation.
---

# JSON API

## Repository

### Pause / unpause the merging of PRs on a repository.

<mark style="color:green;">`POST`</mark> `https://api.aviator.co/api/v1/repo`

Example:

`curl -X POST -H "Authorization: Bearer <aviator_token>"`\
`-H "Content-Type: application/json"`\
`-d '{"org": "org_name", "name": "repo_name", "paused": true }'`\
`https://api.aviator.co/api/v1/repo/`

#### Request Body

| Name                                     | Type    | Description                                        |
| ---------------------------------------- | ------- | -------------------------------------------------- |
| org<mark style="color:red;">\*</mark>    | String  | Name of the GitHub organization.                   |
| name<mark style="color:red;">\*</mark>   | String  | Name of the repository in the GitHub organization. |
| paused<mark style="color:red;">\*</mark> | Boolean | Whether to pause or unpause the queue              |

{% tabs %}
{% tab title="200: OK Success" %}
```javascript
{
  "org": "ankitjaindce",
  "name": "testrepo",
  "paused": false
}
```
{% endtab %}
{% endtabs %}



### Fetch the list of repositories along with their pause and active status.

<mark style="color:blue;">`GET`</mark> `https://api.aviator.co/api/v1/repo`

The results are paginated with maximum 10 results in every request.

#### Query Parameters

| Name | Type    | Description                 |
| ---- | ------- | --------------------------- |
| page | Integer | page number. Defaults to 1. |

{% tabs %}
{% tab title="200: OK " %}
```
[
    {
        "active": false,
        "name": "public-test",
        "org": "aviator-co",
        "paused": false
    },
    {
        "active": true,
        "name": "testrepo",
        "org": "aviator-co",
        "paused": false
    }
]

```
{% endtab %}
{% endtabs %}

## Branches

### Pause / unpause the merging of PRs for specific base branches.

<mark style="color:green;">`POST`</mark> `https://api.aviator.co/api/v1/branches`

You can specify a glob pattern of base branches to pause or activate the Aviator queue. This ensures that you can continue merging branches to other base branches. You can override to pause / unpause all branches by using the Repository endpoint described above.

Example:

`curl -X POST -H "Authorization: Bearer <aviator_token>"`\
`-H "Content-Type: application/json"`\
`-d '{ "pattern": "release-*", "repository": {"org": "aviator", "name": "`av-demo-release`"}, "paused": true, "paused_message": "This release branch has been paused."}'`\
`https://api.aviator.co/api/v1/branches`

#### Request Body

| Name                                         | Type    | Description                                                             |
| -------------------------------------------- | ------- | ----------------------------------------------------------------------- |
| repository<mark style="color:red;">\*</mark> | Object  | Repository object associated with the branch                            |
| > org<mark style="color:red;">\*</mark>      | String  | Organization associated with the repository                             |
| > name<mark style="color:red;">\*</mark>     | String  | Name of the repository                                                  |
| pattern<mark style="color:red;">\*</mark>    | String  | Glob pattern representing the base branch. E.g. `master` or `release-*` |
| paused<mark style="color:red;">\*</mark>     | Boolean | Whether to pause or unpause the queue                                   |
| paused\_message                              | String  | A customized message to post on the top PR when this branch is paused.  |

{% tabs %}
{% tab title="200: OK " %}
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
{% endtab %}
{% endtabs %}

### Get base branches and their statuses (paused / unpaused)

<mark style="color:blue;">`GET`</mark> `https://api.aviator.co/api/v1/branches`

You can specify a glob pattern of base branches to fetch the status of. If not provided, it will fetch the status of all base branches for a specific repository.\
\
Example:

`curl -H "Authorization: Bearer <aviator_token>"`\
`-H "Content-Type: application/json"`\
`-d '{ "repository": {"org": "aviator", "name": "`av-demo-release`"}, "pattern": "release-*"}'`\
`https://api.aviator.co/api/v1/branches`

#### Query Parameters

| Name                                         | Type   | Description                                                             |
| -------------------------------------------- | ------ | ----------------------------------------------------------------------- |
| repository<mark style="color:red;">\*</mark> | Object | Repository object associated with the branch                            |
| > org <mark style="color:red;">\*</mark>     | String | Organization associated with the repository                             |
| > name<mark style="color:red;">\*</mark>     | String | Name of the repository                                                  |
| pattern                                      | String | Glob pattern representing the base branch. E.g. `master` or `release-*` |

{% tabs %}
{% tab title="200: OK Success" %}
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
{% endtab %}
{% endtabs %}

## PullRequest

### Queue or Dequeue a Pull Request

<mark style="color:green;">`POST`</mark> `https://api.aviator.co/api/v1/pull_request`

`curl -X POST -H "Authorization: Bearer <aviator_token>"`\
`-H "Content-Type: application/json"`\
`-d '{"action": "queue", "pull_request": {"number": 1234, "repository": {"name": "repo_name", "org": "org_name"}, "head_commit_sha":" "`69f4404fda48aa2932abfbcb6956a9ccd473b17d`", "affected_targets": ["targetA", "targetB"], "merge_commit_message": {"title": "This is where title goes", "body": "This is where body goes"}}}'`\
`https://api.aviator.co/api/v1/pull_request/`

#### Request Body

| Name                                            | Type          | Description                                                                                                                               |
| ----------------------------------------------- | ------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| action<mark style="color:red;">\*</mark>        | String        | Action taken. Valid options: `update`, `queue` or `dequeue`                                                                               |
| pull\_request<mark style="color:red;">\*</mark> | Object        | PullRequest object representing the PR that is queued.                                                                                    |
| > number<mark style="color:red;">\*</mark>      | String        |                                                                                                                                           |
| > repository<mark style="color:red;">\*</mark>  | Object        | Repository object associated with the PR                                                                                                  |
| > > name<mark style="color:red;">\*</mark>      | String        | Name of the repository                                                                                                                    |
| > > org<mark style="color:red;">\*</mark>       | String        | Organization associated with the repository                                                                                               |
| > head\_commit\_sha                             | String        | Representing the commit SHA of the head of the PR.                                                                                        |
| > affected\_targets                             | List\[String] | Affected targets for the PR. Please see [affected targets](../../mergequeue/concepts/affected-targets/#overview) section for more details |
| > merge\_commit\_message                        | Object        | CommitMessage object                                                                                                                      |
| > > title                                       | String        | Title of merge commit message                                                                                                             |
| > > body                                        | String        | Body of merge commit message                                                                                                              |
| > merge\_commit\_sha                            | String        | _Optional_. Represents the SHA associated with the commit on the mainline generated after the PR was merged.                              |



{% tabs %}
{% tab title="200: OK " %}
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
{% endtab %}
{% endtabs %}

### Request to backport a PR on the specified target branch.

<mark style="color:green;">`POST`</mark> `https://api.aviator.co/api/v1/pull_request/backport`

Example:

`curl -X POST -H "Authorization: Bearer <aviator_token>"`\
`-H "Content-Type: application/json"`\
`-d '{"target_branch": "release-v1", "`source\_pull`": {"number": 1234, "repository": {"name": "repo_name", "org": "org_name"}}}'`\
`https://api.aviator.co/api/v1/pull_request/backport`

#### Request Body

| Name                                             | Type    | Description                                       |
| ------------------------------------------------ | ------- | ------------------------------------------------- |
| target\_branch<mark style="color:red;">\*</mark> | String  | Name of the base branch to backport this PR to    |
| source\_pull<mark style="color:red;">\*</mark>   | Object  | PullRequest object for the current branch         |
| > number<mark style="color:red;">\*</mark>       | Integer | PullRequest number                                |
| > repository<mark style="color:red;">\*</mark>   | Object  | GitHub repository associated with the PullRequest |
| > > org<mark style="color:red;">\*</mark>        | String  | Organization associated with the repository       |
| > > name                                         | String  | Name of the repository                            |

{% tabs %}
{% tab title="200: OK " %}
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
{% endtab %}
{% endtabs %}

### Fetch information of a PR based on the branch name or number

<mark style="color:blue;">`GET`</mark> `https://api.aviator.co/api/v1/pull_request`

Example:

`curl -H "Authorization: Bearer <aviator_token>"`\
`-H "Content-Type: application/json"`\
`https://api.aviator.co/api/v1/pull_request?org=orgname&repo=reponame&branch=branchname`

#### Query Parameters

| Name                                     | Type    | Description                                                                     |
| ---------------------------------------- | ------- | ------------------------------------------------------------------------------- |
| org<mark style="color:red;">\*</mark>    | String  | Organization associated with the repository                                     |
| repo<mark style="color:red;">\*</mark>   | String  | Name of the repository                                                          |
| branch<mark style="color:red;">\*</mark> | String  | Feature branch associated with PR. One of `branch` or `number` must be present. |
| number<mark style="color:red;">\*</mark> | Integer | PR number to fetch. One of `branch` or `number` must be present.                |

{% tabs %}
{% tab title="200: OK Success" %}
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
{% endtab %}
{% endtabs %}

### Fetch information of PRs that are in the queued state

<mark style="color:blue;">`GET`</mark> `https://api.aviator.co/api/v1/pull_request/queued`

Example:

`curl -H "Authorization: Bearer <aviator_token>"`\
`-H "Content-Type: application/json"`\
`https://api.aviator.co/api/v1/pull_request/queued?org=orgname&repo=reponame&base_branch=master`

#### Query Parameters

| Name                                   | Type   | Description                                 |
| -------------------------------------- | ------ | ------------------------------------------- |
| org<mark style="color:red;">\*</mark>  | String | Organization associated with the repository |
| repo<mark style="color:red;">\*</mark> | String | Name of the repository                      |
| base\_branch                           | String | Target branch to fetch queued PRs for       |

{% tabs %}
{% tab title="200: OK Success" %}
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
{% endtab %}
{% endtabs %}

## BotPullRequest

### Fetch information of PRs associated with a provided Bot PullRequest

<mark style="color:blue;">`GET`</mark> `https://api.aviator.co/api/v1/bot_pull_request`

A Bot PR is a draft PR created during parallel mode to validate the CI.

Example:

`curl -H "Authorization: Bearer <aviator_token>"`\
`-H "Content-Type: application/json"`\
`https://api.aviator.co/api/v1/bot_pull_request?org=orgname&repo=reponame&number=1234`

#### Query Parameters

| Name                                     | Type    | Description                                 |
| ---------------------------------------- | ------- | ------------------------------------------- |
| org<mark style="color:red;">\*</mark>    | String  | Organization associated with the repository |
| repo<mark style="color:red;">\*</mark>   | String  | Name of the repository                      |
| number<mark style="color:red;">\*</mark> | Integer | PR number associated with the Bot PR        |

{% tabs %}
{% tab title="200: OK Success" %}
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
{% endtab %}
{% endtabs %}

## Config

### Fetch the current YAML config associated with the given GitHub repository.

<mark style="color:blue;">`GET`</mark> `https://api.aviator.co/api/v1/config`

Example:

`curl -H "Authorization: Bearer <aviator_token>"`\
`-H "Content-Type: application/json"`\
`https://api.aviator.co/api/v1/config?org=orgname&repo=reponame`

#### Query Parameters

| Name                                   | Type   | Description                                         |
| -------------------------------------- | ------ | --------------------------------------------------- |
| org<mark style="color:red;">\*</mark>  | String | Organization associated with the GitHub repository. |
| repo<mark style="color:red;">\*</mark> | String | Name of the GitHub repository.                      |

{% tabs %}
{% tab title="200: OK " %}
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
{% endtab %}
{% endtabs %}

### Change the YAML config associated with the given GitHub repository.

<mark style="color:green;">`POST`</mark> `https://api.aviator.co/api/v1/config`

The request accepts the payload as the raw string YAML format, and returns back response in a JSON format.



Example:

`curl -X POST --data-raw "$(cat /Users/aviator-demo/config.text)" -H "Authorization: Bearer <API_TOKEN>" "https://api.aviator.co/api/v1/config?repo=repo_name&org=org_name"`

#### Query Parameters

| Name                                   | Type   | Description                                      |
| -------------------------------------- | ------ | ------------------------------------------------ |
| org<mark style="color:red;">\*</mark>  | String | Organization associated with a GitHub repository |
| repo<mark style="color:red;">\*</mark> | String | Name of the GitHub repository.                   |

{% tabs %}
{% tab title="200: OK " %}
```
{
  "success": true
}
```
{% endtab %}

{% tab title="400: Bad Request " %}
```
{
    "errors": [
        "mergeRules -> mergeMode -> parallelMode -> updateBeforeRequeue: value could not be parsed to a boolean",
        "mergeRules -> mergeMode -> parallelMode -> checkMergeabilityToQueue: value could not be parsed to a boolean"
    ],
    "success": false
}
```
{% endtab %}
{% endtabs %}

## Config Change

### Fetch the history of config changes associated with a given repository.

<mark style="color:blue;">`GET`</mark> `https://api.aviator.co/api/v1/config/history`

Returns a list of config history events as diffs of changes. `repo` and `org` must be provided.

Example:

`curl -H "Authorization: Bearer <aviator_token>"`\
`-H "Content-Type: application/json"`\
`https://api.aviator.co/api/v1/config/history?org=orgname&repo=reponame`

#### Query Parameters

| Name                                   | Type    | Description                                                |
| -------------------------------------- | ------- | ---------------------------------------------------------- |
| org<mark style="color:red;">\*</mark>  | String  | Organization associated with the repository                |
| repo<mark style="color:red;">\*</mark> | String  | Name of the repository                                     |
| start                                  | String  | UTC Start date in _YYYY-MM-DD_ format. Example: 2021-07-21 |
| end                                    | String  | UTC End date in _YYYY-MM-DD_ format. Example: 2021-07-21   |
| page                                   | Integer | Page number. Defaults to 1.                                |

{% tabs %}
{% tab title="200: OK Success" %}
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
{% endtab %}
{% endtabs %}

The `modified_by` property contains email and gh\_username. If the config was modified from the Dashboard, `email` of the user would be present, and if the config was modified from the GitHub repo change, a `gh_username` would be present. `commit_sha` property may also be only present if the change was made from the GitHub repository.

## Analytics

### Get list of analytics objects representing statistics on a daily basis.

<mark style="color:blue;">`GET`</mark> `https://api.aviator.co/api/v1/analytics`

Example:

`curl -H "Authorization: Bearer <aviator_token>"`\
`-H "Content-Type: application/json"`\
`https://api.aviator.co/api/v1/analytics/?start=2021-07-14&end=2021-07-21&timezone=America/Los_Angeles&repo=orgname/reponame`

#### Query Parameters

<table><thead><tr><th width="184">Name</th><th width="139">Type</th><th>Description</th></tr></thead><tbody><tr><td>start</td><td>String</td><td>UTC Start date in <em>YYYY-MM-DD</em> format. Example: 2021-07-21</td></tr><tr><td>end</td><td>String</td><td>UTC End date in <em>YYYY-MM-DD</em> format. Example: 2021-07-21</td></tr><tr><td>repo<mark style="color:red;">*</mark></td><td>String</td><td>Name of the GitHub repo, in the format: <em>orgname/reponame</em></td></tr><tr><td>timezone</td><td>String</td><td>Standard tz format string. Defaults to account timezone. Example: America/Los_Angeles</td></tr></tbody></table>

{% tabs %}
{% tab title="200: OK Success" %}
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
{% endtab %}
{% endtabs %}

## Queue

## Get live statistics about the state of the merge queue

<mark style="color:blue;">`GET`</mark> `https://api.aviator.co/api/v1/queue/stats`

Currently this endpoint only reports statistics about the depth of the queue.

#### Query Parameters

| Name                                   | Type   | Description                                       |
| -------------------------------------- | ------ | ------------------------------------------------- |
| org<mark style="color:red;">\*</mark>  | String | The GitHub organization that the repo belongs to. |
| repo<mark style="color:red;">\*</mark> | String | The name of the GitHub repo.                      |

{% tabs %}
{% tab title="200: OK Success" %}
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
{% endtab %}
{% endtabs %}

## User actions

{% hint style="info" %}
This API is only available in Enterprise plan.
{% endhint %}

Fetch the actions performed by the users on the Aviator web app dashboard (audit logs). The results returned are ordered reverse chronologically.

<mark style="color:blue;">`GET`</mark> `https://api.aviator.co/api/v1/user_actions`

Example:

`curl -H "Authorization: Bearer <aviator_token>"`\
`-H "Content-Type: application/json"`\
`https://api.aviator.co/api/v1/user_actions/?page=2&entity=user&action=user_removed`

#### Query parameters

<table><thead><tr><th width="149">Name</th><th width="132">Type</th><th>Description</th></tr></thead><tbody><tr><td>entity</td><td>String</td><td>Represents the entity type that is changed. Currently supported entities are <code>user</code>, <code>merge_queue</code>, <code>repository</code>, <code>account</code>, <code>billing</code>, <code>integrations</code> and <code>flexreview</code></td></tr><tr><td>action</td><td>String</td><td>The type of action performed by the user.</td></tr><tr><td>page</td><td>Number</td><td>Page number</td></tr></tbody></table>

**Response**

The query returns a list of user actions with the following properties.

<table><thead><tr><th width="188">Name</th><th width="132">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>action</code></td><td>String</td><td>The type of action performed by the user</td></tr><tr><td><code>actor</code></td><td>String</td><td>The email address of the user who performed the action</td></tr><tr><td><code>entity</code></td><td>String</td><td>The entity type that has changed.</td></tr><tr><td><code>target</code></td><td>String</td><td><em>Optional</em>. Represents the target entity that has changed. It could be null if not applicable. E.g. it will be null for entity type <code>account</code> or <code>integrations</code></td></tr><tr><td><code>timestamp</code></td><td>String</td><td>ISO timestamp representing the time with timezone</td></tr></tbody></table>

{% tabs %}
{% tab title="200" %}
```json
[
    {
        "action": "user_removed",
        "actor": "ankit@aviator.co",
        "entity": "user",
        "target": "heeju@aviator.co",
        "timestamp": "2024-10-02T00:45:05.881726+00:00"
    },
    {
        "action": "queue_config_changed",
        "actor": "ankit+masaya-demo@aviator.co",
        "entity": "merge_queue",
        "target": "aviator-staging-testing/release-testing",
        "timestamp": "2024-10-02T00:31:46.471689+00:00"
    }
]
```
{% endtab %}
{% endtabs %}

