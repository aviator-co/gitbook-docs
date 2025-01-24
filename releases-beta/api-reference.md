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
```
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
```
{
  "error": "Release environment not found"
}
```
{% endtab %}

{% tab title="404" %}
```
{
  "error": "Release not found"
}
```
{% endtab %}
{% endtabs %}
