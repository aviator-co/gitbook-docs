---
description: >-
  This reference page describes the usage of REST API for Runbooks. Get
  instructions on how to create a runbook, check its status, and more.
---

# API Reference

This document describes the usage of REST API for Runbooks. You can also use [<mark style="color:blue;">GraphQL API</mark>](../api/reference/graphql.md) for composite queries.

## Create a Runbook

<mark style="color:green;">`POST`</mark> `/api/v1/runbook`

This creates a new Runbook and triggers its execution asynchronously. The runbook is created using the provided prompt and repository, and the response includes the runbook number and a URL to track its progress.

**Headers**

| Name          | Value              |
| ------------- | ------------------ |
| Content-Type  | `application/json` |
| Authorization | `Bearer <token>`   |

**Body**

| Name            | Type                | Description                                                                                                                                                |
| --------------- | ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `repository`    | Object              | Repository to run the runbook against. Must contain `org` (GitHub organization name) and `name` (repository name).                                         |
| `prompt`        | String              | Task description for the runbook.                                                                                                                          |
| `title`         | String. _Optional_  | Title for the runbook. If not provided, a title will be auto-generated.                                                                                    |
| `oneshot`       | Boolean. _Optional_ | Whether to run in [one-shot mode](concepts/one-shot-mode.md). Default is `true`.                                                                           |
| `draft`         | Boolean. _Optional_ | Whether to create pull requests as drafts. If not provided, defaults to the project configuration.                                                         |
| `pr_mode`       | String. _Optional_  | PR creation mode. Possible values: `manual`, `single_pr`, `stacked_pr`. If not provided, defaults to the project configuration.                            |
| `target_branch` | String. _Optional_  | Base branch for the runbook. If not provided, defaults to the repository's default branch.                                                                 |

**Request body example**

```json
{
  "repository": {
    "org": "acme-corp",
    "name": "backend"
  },
  "prompt": "Upgrade all React dependencies to v19",
  "title": "React v19 upgrade",
  "oneshot": true,
  "draft": true,
  "pr_mode": "single_pr",
  "target_branch": "develop"
}
```

**Response**

If successful, HTTP 202 response is returned back since the runbook runs asynchronously. The response uses the same schema as the [Get Runbook Status](api-reference.md#get-runbook-status) endpoint. At creation time, step counts are all zero and `pull_requests` is empty.

{% tabs %}
{% tab title="202" %}
```json
{
  "runbook_number": 42,
  "url": "https://app.aviator.co/r/42",
  "status": "generating_steps",
  "steps": {
    "total": 0,
    "not_started": 0,
    "in_progress": 0,
    "completed": 0,
    "failed": 0,
    "queued": 0
  },
  "pull_requests": []
}
```
{% endtab %}

{% tab title="400" %}
```json
{
  "error": "bad-request",
  "message": "Invalid request body"
}
```
{% endtab %}

{% tab title="402" %}
```json
{
  "error": "insufficient-credits",
  "message": "Account has no remaining runbook credits."
}
```
{% endtab %}

{% tab title="404" %}
```json
{
  "error": "not-found",
  "message": "Repository not found."
}
```
{% endtab %}
{% endtabs %}

## Get Runbook Status

<mark style="color:green;">`GET`</mark> `/api/v1/runbook/<runbook_number>`

Retrieve the current status of a Runbook by its number. The response includes the aggregate status, step counts, and any associated pull requests.

**Headers**

| Name          | Value            |
| ------------- | ---------------- |
| Authorization | `Bearer <token>` |

**Parameters**

| Name              | Description                     |
| ----------------- | ------------------------------- |
| `runbook_number`  | The number identifying the runbook. |

**Response**

If successful, HTTP 200 response is returned back.

{% tabs %}
{% tab title="200" %}
```json
{
  "runbook_number": 42,
  "url": "https://app.aviator.co/r/42",
  "status": "in_progress",
  "steps": {
    "total": 5,
    "not_started": 1,
    "in_progress": 2,
    "completed": 1,
    "failed": 0,
    "queued": 1
  },
  "pull_requests": [
    {
      "number": 1234,
      "url": "https://github.com/acme-corp/backend/pull/1234",
      "step_numbers": ["1.1", "1.2"]
    },
    {
      "number": 1235,
      "url": "https://github.com/acme-corp/backend/pull/1235",
      "step_numbers": ["2.1"]
    }
  ]
}
```
{% endtab %}

{% tab title="404" %}
```json
{
  "error": "not-found",
  "message": "Runbook not found."
}
```
{% endtab %}
{% endtabs %}

### Response schema

Both the Create and Get endpoints return the same response schema.

| Name                          | Type   | Description                                                                                |
| ----------------------------- | ------ | ------------------------------------------------------------------------------------------ |
| `runbook_number`              | Integer | The unique number identifying the runbook.                                                |
| `url`                         | String | URL to view the runbook in the Aviator dashboard.                                          |
| `status`                      | String | Aggregate status of the runbook. See [Status values](api-reference.md#status-values) below. |
| `steps`                       | Object | Breakdown of step counts by status. Only counts leaf steps (actual execution units).       |
| `steps.total`                 | Integer | Total number of leaf steps.                                                               |
| `steps.not_started`           | Integer | Steps that have not started.                                                              |
| `steps.in_progress`           | Integer | Steps currently executing.                                                                |
| `steps.completed`             | Integer | Steps that completed successfully.                                                        |
| `steps.failed`                | Integer | Steps that failed.                                                                        |
| `steps.queued`                | Integer | Steps queued for execution.                                                               |
| `pull_requests`               | Array  | Pull requests created by the runbook, deduplicated by PR number.                           |
| `pull_requests[].number`      | Integer | GitHub pull request number.                                                               |
| `pull_requests[].url`         | String | URL of the GitHub pull request.                                                           |
| `pull_requests[].step_numbers`| Array  | List of step numbers (e.g., `"1.1"`, `"2.3"`) associated with this pull request.          |

### Status values

The `status` field is computed from leaf step statuses at query time.

| Status                 | Condition                                                                           |
| ---------------------- | ----------------------------------------------------------------------------------- |
| `generating_steps`     | No steps exist yet (the runbook is still generating its execution plan).            |
| `not_started`          | All steps are `not_started`.                                                        |
| `in_progress`          | At least one step is `in_progress`.                                                 |
| `partially_completed`  | Mix of `completed`/`queued` and `not_started` steps, with none in-progress or failed. |
| `failed`               | At least one step has `failed` and none are `in_progress`.                          |
| `completed`            | All steps are `completed`.                                                          |
