---
description: >-
  This reference page describes the usage of REST API for Release management.
  Get instructions on how to create a release, create and update deployment, and
  more.
---

# API Reference

This document describes the usage of REST API for Release management. You can also use [<mark style="color:blue;">GraphQL API</mark>](../api/reference/graphql.md) for composite queries.

## Create a Release

<mark style="color:green;">`POST`</mark> `/api/releases/<project_name>/releases/<release_candidate_version>`

This creates a new [<mark style="color:blue;">Release</mark>](concepts/terminology.md#release) and an associated [<mark style="color:blue;">Release Candidate</mark>](concepts/terminology.md#release-candidate) with the provided commit SHA.

**Parameters**

<table><thead><tr><th width="223">Name</th><th>Description</th></tr></thead><tbody><tr><td><code>project_name</code></td><td>Name of the <a href="api-reference.md#create-a-new-release"><mark style="color:blue;">Release Project</mark></a><mark style="color:blue;">.</mark> Case sensitive.</td></tr><tr><td><code>release_candidate_version</code></td><td>Release candidate version string represented as: <code>release-version-rcX</code> when X is an integer. Note that <code>-rcX</code> is a required suffix for a validate release candidate version.</td></tr></tbody></table>

**Headers**

| Name          | Value              |
| ------------- | ------------------ |
| Content-Type  | `application/json` |
| Authorization | `Bearer <token>`   |

**Body**

| Name                     | Type                | Description                                                                                                                                                                                                         |
| ------------------------ | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `repo`                   | String              | Repository associated with the Release. String of the format: `org/repo_name`                                                                                                                                       |
| `commit`                 | String              | Head commit SHA to cut a release at.                                                                                                                                                                                |
| `trigger_build_workflow` | Boolean. _Optional_ | If set to `true`, it will start a build action that is configured in the settings. When set to false, the release candidate is created and marked as ready without triggering the build action. Default is `false`. |

**Response**

If successful, HTTP 201 response is returned back.

{% tabs %}
{% tab title="201" %}
```
```
{% endtab %}

{% tab title="400" %}
```json
{
  "error": "Invalid release candidate version"
}
```
{% endtab %}

{% tab title="400" %}
```json
{
  "error": "Invalid commit hash"
}
```
{% endtab %}

{% tab title="404" %}
```json
{
  "error": "GitHub repository not found"
}
```
{% endtab %}
{% endtabs %}

## Create a Deployment

<mark style="color:green;">`POST`</mark> `/api/releases/<project_name>/environments/<env_name>/deployments`

This creates a new [<mark style="color:blue;">Deployment</mark>](concepts/terminology.md#deployment) for the provided [<mark style="color:blue;">Release Candidate</mark>](concepts/terminology.md#release-candidate) and [<mark style="color:blue;">Environment</mark>](concepts/terminology.md#environment). This workflow will also trigger the appropriate deployment workflow configured for that environment.

**Headers**

| Name          | Value              |
| ------------- | ------------------ |
| Content-Type  | `application/json` |
| Authorization | `Bearer <token>`   |

**Parameters**

<table><thead><tr><th width="198">Name</th><th>Description</th></tr></thead><tbody><tr><td><code>project_name</code></td><td>Name of the <a href="api-reference.md#create-a-new-release"><mark style="color:blue;">Release Project</mark></a><mark style="color:blue;">.</mark> Case sensitive.</td></tr><tr><td><code>env_name</code></td><td>Name of the <a href="how-to-guides/configuring-environments.md"><mark style="color:blue;">Environment</mark></a> within the Release Project. Case sensitive.</td></tr></tbody></table>

**Body**

| Name                        | Type   | Description                                                                                                                                                                                                                                                                                                             |
| --------------------------- | ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `release_candidate_version` | String | Release candidate version associated with the [<mark style="color:blue;">Release Candidate</mark>](concepts/terminology.md#release-candidate) that will be deployed. String represented as: `release-version-rcX` when X is an integer. Note that `-rcX` is a required suffix for a validate release candidate version. |

**Response**

If successful, HTTP 201 response is returned back.

{% tabs %}
{% tab title="201" %}
```json
```
{% endtab %}

{% tab title="400" %}
```json
{
  "error": "Invalid release candidate version"
}
```
{% endtab %}

{% tab title="404" %}
```json
{
  "error": "Release environment not found"
}
```
{% endtab %}

{% tab title="404" %}
```json
{
  "error": "Release not found"
}
```
{% endtab %}
{% endtabs %}

## Update Deployment status

<mark style="color:green;">`PATCH`</mark> `/api/releases/<project_name>/environments/<env_name>/deployments`

Update the status of the [<mark style="color:blue;">Deployment</mark>](concepts/terminology.md#deployment) once it's created. If a Deployment doesn't already exist for the given Release Candidate version and the Environment, a new deployment is also created.

This can be used for a custom CD pipeline to send the deployment status to Aviator.

{% hint style="info" %}
This method does not trigger the configured deployment workflow.
{% endhint %}

**Headers**

| Name          | Value              |
| ------------- | ------------------ |
| Content-Type  | `application/json` |
| Authorization | `Bearer <token>`   |

**Parameters**

<table><thead><tr><th width="198">Name</th><th>Description</th></tr></thead><tbody><tr><td><code>project_name</code></td><td>Name of the <a href="api-reference.md#create-a-new-release"><mark style="color:blue;">Release Project</mark></a><mark style="color:blue;">.</mark> Case sensitive.</td></tr><tr><td><code>env_name</code></td><td>Name of the <a href="how-to-guides/configuring-environments.md"><mark style="color:blue;">Environment</mark></a> within the Release Project. Case sensitive.</td></tr></tbody></table>

**Body**

| Name                        | Type   | Description                                                                                                                                                                                                                                                                                                             |
| --------------------------- | ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `release_candidate_version` | string | Release candidate version associated with the [<mark style="color:blue;">Release Candidate</mark>](concepts/terminology.md#release-candidate) that will be deployed. String represented as: `release-version-rcX` when X is an integer. Note that `-rcX` is a required suffix for a validate release candidate version. |
| `status`                    | string | <p>Possible values:<br>- <code>pending</code><br>- <code>in_progress</code><br>- <code>failure</code><br>- <code>canceling</code><br>- <code>canceled</code><br>- <code>unknown</code></p>                                                                                                                              |

**Response**

If successful, HTTP 200 response is returned back. The API returns a 200 response even when a new deployment is created.

{% tabs %}
{% tab title="200" %}
```json
```
{% endtab %}

{% tab title="400" %}
```json
{
  "error": "Invalid release candidate version"
}
```
{% endtab %}

{% tab title="404" %}
```json
{
  "error": "Release environment not found"
}
```
{% endtab %}

{% tab title="404" %}
```json
{
  "error": "Release not found"
}
```
{% endtab %}
{% endtabs %}

## List deployments

<mark style="color:green;">`GET`</mark> `/api/releases/<project_name>/environments/<env_name>/deployments`

List recent [<mark style="color:blue;">Deployments</mark>](concepts/terminology.md#deployment) for a given [<mark style="color:blue;">Environment</mark>](concepts/terminology.md#environment). Results are paginated and ordered by most recent first.

**Headers**

| Name          | Value              |
| ------------- | ------------------ |
| Authorization | `Bearer <token>`   |

**Parameters**

<table><thead><tr><th width="198">Name</th><th>Description</th></tr></thead><tbody><tr><td><code>project_name</code></td><td>Name of the <a href="api-reference.md#create-a-new-release"><mark style="color:blue;">Release Project</mark></a><mark style="color:blue;">.</mark> Case sensitive.</td></tr><tr><td><code>env_name</code></td><td>Name of the <a href="how-to-guides/configuring-environments.md"><mark style="color:blue;">Environment</mark></a> within the Release Project. Case sensitive.</td></tr></tbody></table>

**Query Parameters**

| Name       | Type              | Description                                                    |
| ---------- | ----------------- | -------------------------------------------------------------- |
| `page`     | Integer. _Optional_ | Page number for pagination. Defaults to `1`.                  |
| `per_page` | Integer. _Optional_ | Number of results per page. Defaults to `10`. Maximum `100`.  |

**Response**

If successful, HTTP 200 response is returned back.

{% tabs %}
{% tab title="200" %}
```json
[
    {
      "actor": "User email or None",
      "deploy_end_at": "The timestamp that the deployment process ended at.",
      "deploy_start_at": "The timestamp that the deployment process started at.",
      "environment_name": "Release environment name",
      "error_message": "Error message associated with the deployment, or null.",
      "from_release_candidate_version": "The RC version of the previous release that this environment is transitioning from or None",
      "from_release_version": "The version of the previous release that this environment is transitioning from or None",
      "id": "Deployment id",
      "release_candidate_version": "Deployment release candidate version",
      "release_version": "Deployment release version",
      "status": "The status of the deployment."
    },
    ...
]
```
{% endtab %}

{% tab title="404" %}
```json
{
  "error": "Release environment not found"
}
```
{% endtab %}

{% tab title="404" %}
```json
{
  "error": "Release project not found"
}
```
{% endtab %}
{% endtabs %}

## Rollback

<mark style="color:green;">`POST`</mark> `/api/releases/<project_name>/environments/<env_name>/rollback`

Roll back an [<mark style="color:blue;">Environment</mark>](concepts/terminology.md#environment) to a previous [<mark style="color:blue;">Release Candidate</mark>](concepts/terminology.md#release-candidate). If the most recent deployment was successful, the environment is rolled back to the version that was running before it. If the most recent deployment failed, the last known successful version is re-deployed. Any active deployments are canceled before the rollback is triggered.

**Headers**

| Name          | Value              |
| ------------- | ------------------ |
| Content-Type  | `application/json` |
| Authorization | `Bearer <token>`   |

**Parameters**

<table><thead><tr><th width="198">Name</th><th>Description</th></tr></thead><tbody><tr><td><code>project_name</code></td><td>Name of the <a href="api-reference.md#create-a-new-release"><mark style="color:blue;">Release Project</mark></a><mark style="color:blue;">.</mark> Case sensitive.</td></tr><tr><td><code>env_name</code></td><td>Name of the <a href="how-to-guides/configuring-environments.md"><mark style="color:blue;">Environment</mark></a> within the Release Project. Case sensitive.</td></tr></tbody></table>

**Body**

| Name    | Type               | Description                                                                                |
| ------- | ------------------ | ------------------------------------------------------------------------------------------ |
| `force` | Boolean. _Optional_ | If set to `true`, allows rollback even when the environment is frozen. Default is `false`. |

**Response**

If successful, HTTP 201 response is returned back with the created deployment.

{% tabs %}
{% tab title="201" %}
```json
{
  "actor": "User email or null",
  "deploy_end_at": null,
  "deploy_start_at": "ISO 8601 timestamp of when the deployment was triggered.",
  "environment_name": "Release environment name",
  "error_message": null,
  "from_release_candidate_version": "The RC version of the release this environment is transitioning from, or null",
  "from_release_version": "The version of the release this environment is transitioning from, or null",
  "id": "Deployment id",
  "release_candidate_version": "Deployment release candidate version",
  "release_version": "Deployment release version",
  "status": "pending"
}
```
{% endtab %}

{% tab title="400" %}
```json
{
  "error": "Environment is frozen"
}
```
{% endtab %}

{% tab title="404" %}
```json
{
  "error": "Release project not found"
}
```
{% endtab %}

{% tab title="404" %}
```json
{
  "error": "Release environment not found"
}
```
{% endtab %}

{% tab title="404" %}
```json
{
  "error": "No previous deployment to roll back from"
}
```
{% endtab %}
{% endtabs %}
