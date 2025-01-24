---
description: Use Aviator's GraphQL API to access pull request data.
---

# GraphQL API Quickstart

This tutorial will show you how to use the Aviator GraphQL API to access pull request data from your Aviator account.

## 1. Create a user access token

[<mark style="color:blue;">Create a user access token from the Aviator webapp.</mark>](https://app.aviator.co/account/apitoken)

* Enter `GraphQL tutorial` for the token name.
* Select `30 days` for the token expiration.

This should be in the format `av_uat_abcdefghijklmnopqrstuvwxyz`. Make sure to save this value as it can't be accessed again after leaving the token creation page.

In your terminal, set the `AV_API_TOKEN` environment variable to the value of the token you just created:

```shell
# IMPORTANT!
# Replace this value with the token you just created.
AV_API_TOKEN="av_uat_abcdefghijklmnopqrstuvwxyz"
```

## 2. Make a simple GraphQL request

Make sure that you've set the `AV_API_TOKEN` environment variable to the value of the token you created in step 1. Requests can be made with any HTTP client.

A simple curl request to fetch the full name of the user making the request looks like:

```python3
import os
import requests

query = """
{
    viewer {
        fullName
    }
}
"""

av_api_token = os.environ["AV_API_TOKEN"]
res = requests.post(
    "https://api.aviator.co/graphql",
    json={"query": query},
    headers={"Authorization": "Bearer "+av_api_token},
)
res.raise_for_status()
print(res.json())
```

The output should be similar to:

```json
{
  "data": {
    "viewer": {
      "fullName": "John Doe"
    }
  }
}
```

## 3. Fetch data about a pull request

The following example fetches the title and current status of a pull request. Make sure to change the `owner`, `name`, and `number` variables to match your desired repository and pull request.

```python3
import os
import requests

query = """
query ($owner: String!, $name: String!, $number: Int!) {
    githubRepository(owner: $owner, name: $name) {
        pullRequest(number: $number) {
            title
            status
        }
    }
}
"""

# IMPORTANT!
# Change these values to match your repository and pull request.
variables = {
    "owner": "my-org",
    "name": "my-repo",
    "number": 1234,
}

av_api_token = os.environ["AV_API_TOKEN"]
res = requests.post(
    "https://api.aviator.co/graphql",
    json={"query": query, "variables": variables},
    headers={"Authorization": "Bearer "+av_api_token},
)
res.raise_for_status()
print(res.json())
```

The output should be similar to:

```json
{
  "data": {
    "githubRepository": {
      "pullRequest": {
        "title": "Update README.md",
        "status": "MERGED"
      }
    }
  }
}
```

## Learn more

* See the official [<mark style="color:blue;">Introduction to GraphQL</mark>](https://graphql.org/learn/) tutorial to learn more about GraphQL.
* [<mark style="color:blue;">GraphQL API reference</mark>](reference/graphql.md)
