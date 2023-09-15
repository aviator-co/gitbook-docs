# Webhooks

Your application can use webhooks to subscribe to events happening on your Aviator account.

When configuring a webhook, you can use the UI to choose which event actions will send you payloads. Only subscribing to the specific events you plan on handling limits the number of HTTP requests to your server. You can also subscribe to all current and future event actions. You can change the list of subscribed events anytime.

Each event corresponds to a certain set of actions that can happen on your organization’s repository. For example, if you subscribe to the merge failure event you'll receive detailed payloads every time a PR fails to merge.

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
