---
description: >-
  Discover the Global CI Validation setting in MergeQueue ChangeSets. It enables
  Aviator to issue webhooks you can use to trigger CI tests for pre-merge
  checks.
---

# Global CI Validation

Along with the CI on each individual change, you may have CI tests you would like to run on the ChangeSet as a whole before the PRs are merged. These tests can target integration-level functionality to test that all these PRs work together. Aviator offers this using a global CI framework. When you enable this setting, Aviator issues webhooks that you can use to trigger CI tests for these pre-merge checks on a ChangeSet.

## CI Webhook

Once all the PRs are added to the ChangeSet, you can trigger a Global CI by clicking the "Start CI" button on the ChangeSet page. This will send a webhook to the configured Webhook endpoint. The webhook will provide information about the related repos, branch names, and SHAs to be used for the CI. The payload will be in json format with the following parameters:

### Webhook Payload

{% tabs %}
{% tab title="Parameters" %}
| Parameter              | Description                                                                                                                                                               |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **change\_set\_id**    | _Integer_. Identifier for ChangeSet.                                                                                                                                      |
| **status\_run\_id**    | _Integer_. Represents a unique ID for the status run. Each time the webhook is triggered, a new Status Run ID is issued. This will be used to track the status of the CI. |
| **pull\_requests**     | A list of objects representing each PullRequest in the ChangeSet. Each object has the following parameters below:                                                         |
| **>number**            | _Integer_. PR Number associated with the action.                                                                                                                          |
| **>base\_branch**      | _String_. Name of the base branch associated with the PR.                                                                                                                 |
| **>head\_branch**      | _String_. Name of the head branch associated with the PR.                                                                                                                 |
| **>commit\_sha**       | _String_. The SHA of the latest commit on the branch.                                                                                                                     |
| **>author**            | _String_. The GitHub username of the PR author.                                                                                                                           |
| **>repo**              | _String_. The GitHub repo name represented as _org\_name/repo\_name_.                                                                                                     |
| **owner**              | The owner of the ChangeSet. This typically represents the user who created the ChangeSet. Contains the following parameter:                                               |
| **>email**             | _String_. The email address of the owner.                                                                                                                                 |
| **custom\_attributes** | _Object_. A Dictionary of key-values as specified in the global and local parameters.                                                                                     |


{% endtab %}

{% tab title="Example" %}
```json
{
  "action": "changeset_start_ci",
  "change_set_id": 100,
  "status_run_id": 150,
  "pull_requests": [
    {
      "number": 1196,
      "head_branch": "mq-25108-1",
      "base_branch": "master",
      "commit_sha": "6705fd2483b1ceaaaa10bb4c34517c38d9fb6a02",
      "author": "mq-demo",
      "repo": "aviator/mergequeue-demo"
    },
    {
      "number": 4114,
      "head_branch": "mq-89706-1",
      "base_branch": "master",
      "commit_sha": "abf02ccc860eecf2d90adf7ca39d72f25479228f",
      "author": "mq-demo",
      "repo": "aviator/mergequeue-changeset"
    }
  ],
  "owner": {
    "email": "demo@mergequeue.com"
  },
  "custom_attributes": {
    "hello": "world",
    "trunk": "master"
  }
}
```


{% endtab %}
{% endtabs %}

{% hint style="warning" %}
Note: This webhook should return a valid HTTP 200 response. If an invalid status code is returned, Aviator will consider the CI as "failed".
{% endhint %}

{% hint style="info" %}
Note: The webhook payload only includes PRs that have been added to the ChangeSet. There will be no associated entry in the webhook payload for PRs that are not included in the ChangeSet.
{% endhint %}

## Global CI Status Check

Once the global CI is triggered, your service should report the status check back using the `status_run` POST endpoint:

## Report CI global status check

<mark style="color:green;">`POST`</mark> `https://api.aviator.co/api/v1/status_run`

Example:

`curl -X POST`\
`-H "Authorization: Bearer "`\
`-H "Content-Type: application/json"`\
`https://api.aviator.co/api/v1/status_run`\
`-d '{ "status_run_id": 8, "state": "success", "name": "full-build", "message": "Build successful", "target_url": "https://example.com/full-build/8" }'`

#### Query Parameters

| Name                                              | Type    | Description                                                                                                                                                    |
| ------------------------------------------------- | ------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| status\_run\_id<mark style="color:red;">\*</mark> | Integer | Represents a unique ID for the status run. Each time the webhook is triggered, a new Status Run ID is issued. This will be used to track the status of the CI. |
| state<mark style="color:red;">\*</mark>           | String  | Should be error, failure, pending or success.                                                                                                                  |
| name<mark style="color:red;">\*</mark>            | String  | Name of the status check to be displayed in the Aviator dashboard.                                                                                             |
| message                                           | String  | Message describing the status.                                                                                                                                 |
| target\_url                                       | String  | A URL associated with the status run. This will be linked from the Aviator dashboard for users to click on for details.                                        |

{% tabs %}
{% tab title="200: OK Success" %}
```javascript
{
  "change_set_id": 2,
  "message": "Build successful",
  "name": "full-build",
  "state": "success",
  "status_run_id": 8,
  "target_url": "https://example.com/full-build/8"
}
```
{% endtab %}
{% endtabs %}

### Multiple Global CI Checks

You can run multiple global status checks and report them separately for the same status\_run\_id using unique name properties. Each of these unique checks are reported back as status checks on every PR in the ChangeSet.
