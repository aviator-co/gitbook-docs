---
description: >-
  You can use a set of Aviator APIs and webhooks to build custom workflows. Get
  instructions on how to queue / dequeue via API in our manual guide.
---

# Queue / Dequeue via API

Aviator provides a set of [<mark style="color:blue;">APIs</mark>](https://docs.aviator.co/reference/api-documentation) and [<mark style="color:blue;">webhooks</mark>](https://docs.aviator.co/reference/webhooks) that you can leverage to build custom workflows. In addition, you can use the [<mark style="color:blue;">Pilot automated workflows</mark>](https://docs.aviator.co/reference/pilot-automated-actions) to define your custom logic.

For instance, you can use an API to queue or dequeue a PR. You can use this to automatically trigger a queue event on an external event happening.

```bash
curl -X POST \
    -H "Authorization: Bearer <aviator_token>" \
    -H "Content-Type: application/json" \
    -d '{"action": "queue",
         "pull_request": {
            "number": 1234,
            "repository": {"name": "repo_name", "org": "org_name"},
            "head_commit_sha":" "69f4404fda48aa2932abfbcb6956a9ccd473b17d",
           "merge_commit_message": {
                "title": "This is where title goes",
                "body": "This is where body goes"
            }
          }
       }’ \
https://api.aviator.co/api/v1/pull_request/
```

Likewise, you can dequeue a PR by using `"action": "dequeue"` in the same API request.
