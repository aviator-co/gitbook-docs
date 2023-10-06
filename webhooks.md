# Webhooks

Your application can use webhooks to subscribe to events happening on your Aviator account.

When configuring a webhook, you can use the UI to choose which event actions will send you payloads. Only subscribing to the specific events you plan on handling limits the number of HTTP requests to your server. You can also subscribe to all current and future event actions. You can change the list of subscribed events anytime.

Each event corresponds to a certain set of actions that can happen on your organization’s repository. For example, if you subscribe to the merge failure event you'll receive detailed payloads every time a PR fails to merge.

## PullRequest events

### Payload

Each PullRequest webhook event payload contains the following properties.&#x20;

| Key                    | Description                                                                                                                                                                                                    |
| ---------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **action**             | All webhook payloads contain an action property that contains the specific activity that triggered the event.                                                                                                  |
| **repository**         | Name of GitHub repository associated with the action.                                                                                                                                                          |
| **organization**       | Name of the GitHub organization associated with the action.                                                                                                                                                    |
| **pr\_number**         | _Integer_. PR Number associated with the action.                                                                                                                                                               |
| **author**             | GitHub handle of the author of the PR.                                                                                                                                                                         |
| **status**             | Current status of the PR. Valid options: open, pending, queued, blocked, merged                                                                                                                                |
| **skip\_line**         | _Boolean_. Represents whether the skip line label is present for the PR.                                                                                                                                       |
| **status\_code**       | _Integer_. Represents the reason for failure, if there was a failure. See [<mark style="color:blue;">Status Codes</mark>](mergequeue/reference/status-codes.md) section for all possible options. |
| **status\_code\_text** | _String_. Represents the reason for failure, if there was a failure. See [<mark style="color:blue;">Status Codes</mark>](mergequeue/reference/status-codes.md) section for all possible options.  |
| **message**            | _Optional_. Present if there is an additional message provided by GitHub on the reason for failure.                                                                                                            |
| **failed\_ci\_list**   | _Optional_. _List_. List of CI names that failed in case of CI failure.                                                                                                                                        |
| **pr\_reset\_count**   | _Optional. Integer_. Number of PRs that were reset due to a reset. This property only exists during a `reset` action.                                                                                          |

### Actions

Below is the list of actions that can be configured in the MQ UI to receive webhook events.

| Name               | When it is triggered                                                                                                                                    |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **opened**         | When the PR is opened.                                                                                                                                  |
| **labeled**        | When the Aviator trigger label was added to a PR.                                                                                                       |
| **queued**         | When the PR is queued. This typically happens when the PR is in an approved state and the Aviator trigger label is associated with the PR.              |
| **dequeued**       | When the Aviator label is manually removed from the PR. It is not reported in case of PR failing to merge.                                              |
| **top\_of\_queue** | When the PR reaches the top of the queue for processing.                                                                                                |
| **merged**         | When the PR is successfully merged.                                                                                                                     |
| **blocked**        | When the PR fails to merge and is blocked. The typical reason for failures can be retrieved from `status_code`, including CI failure or merge conflict. |
| **stuck**          | _(Parallel mode only)_ When a PR is stuck if the original PR is still running the checks after the specified timeout.                                   |
| **reset**          | _(Parallel mode only)_ When a parallel PR queue is reset.                                                                                               |

## Batch events

Batch events contain one or more PullRequests that are queued together in a single batch. These events are triggered regardless of configured batch\_size.

### Payload

<table><thead><tr><th width="369">Key</th><th>Description</th></tr></thead><tbody><tr><td><strong>action</strong></td><td>All webhook payloads contain an action property that contains the specific activity that triggered the event.</td></tr><tr><td><strong>repository</strong></td><td>Name of GitHub repository associated with the action.</td></tr><tr><td><strong>organization</strong></td><td>Name of the GitHub organization associated with the action.</td></tr><tr><td><strong>pull_requests</strong></td><td>A <em>list</em> of pull_<em>requests associated with the batch.</em> Each pull_request object contains <code>pr_number</code>, <code>author</code>, <code>status</code>, <code>skip_line</code>, <code>status_code</code></td></tr><tr><td><strong>message</strong></td><td><em>Optional</em>. Present if there is an additional message provided by GitHub on the reason for failure.</td></tr></tbody></table>

### Actions

| Name              | When it's triggered             |
| ----------------- | ------------------------------- |
| **batch\_merged** | When a batch of PRs are merged. |
| **batch\_failed** | When a batch failed to merge.   |

## Config change event

This webhook is triggered when configuration for a repository is changed. To listen to these webhook events, subscribe to `config_change` webhook.

### Sample payload

```
{
  "action": "config_change",
  "repository": {
    "name": "mergeit",
    "org": "aviator"
  },
  "history": {
    "modified_by": {
      "email": "email@email.com",
      "gh_username": "jainankit"
    },
    "modified_at": "2022-11-16T17:21:41.350499Z"
    "commit_sha": "85d419bbca585f04456083fd98b7858c0f1e4d13",
    "diff": "-     publish: \"always\"\n+     publish: \"ready\"",
  }
}
```

The `modified_by` property contains email and gh\_username. If the config was modified from the Dashboard, `email` of the user would be present, and if the config was modified from the GitHub repo change, a `gh_username` would be present. `commit_sha` property may also be only present if the change was made from the GitHub repository.

## Webhook Signatures

All webhooks sent by Aviator include a digest signature to verify that the webhook was sent by Aviator itself. The signature is included in a `X-Aviator-Signature-SHA256` header and is calculated as the SHA256 of the webhook body using the Aviator account API token as the HMAC key.

In Python, this can be calculated as follows.

{% code title="compare_signature.py" %}
```python
# The exact HTTP body of the webhook
message_body = "{...}"

# The Aviator API token associated with the account
api_token = "av_live_xxxyyyzzz"

# The signature received in the headers of the webhook request
received_signature = "bbbf25d449dc5f1101d36085b7913dfccce6a9307048ed110d4c70afd83eafd5"

import hashlib
import hmac

signature = hmac.new(
    api_token.encode(), message_body.encode(), hashlib.sha256
).hexdigest()

# Use compare_digest rather than == to prevent timing attacks.
if hmac.compare_digest(received_signature, signature):
    print("Signatures match!")
else:
    raise ValueError("Signatures do not match!")
```
{% endcode %}
