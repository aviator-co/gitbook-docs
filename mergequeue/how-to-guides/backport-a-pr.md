---
description: >-
  Get instructions on backporting a PR using the backport slash command or API
  in our how-to guide. Our concise guidelines speed up configuration
  tremendously.
---

# Backport a PR

This action can be taken using the [<mark style="color:blue;">backport slash command</mark>](https://docs.aviator.co/reference/slash-commands#backport) or using the [<mark style="color:blue;">API</mark>](https://docs.aviator.co/reference/api-documentation#pullrequest).

### Slash command

```
/aviator backport <target_branch>
```

### API

```bash
curl -X POST \
  -H "Authorization: Bearer <aviator_token>" \
  -H "Content-Type: application/json" \
  -d '{"target_branch": "release-v3.4", "source_pull": {"number": 1234, "repository": {"name": "repo_name", "org": "org_name"}}}' \
https://api.aviator.co/api/v1/pull_request/backport
```
