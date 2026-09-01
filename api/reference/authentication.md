# API Authentication

The Aviator [<mark style="color:blue;">REST API</mark>](json-api.md) and [<mark style="color:blue;">GraphQL API</mark>](graphql.md) use token-based authentication.

## Account Token

Each Aviator account can have at most one active account-scoped API token. You can view and manage your account API token from the [<mark style="color:blue;">Aviator dashboard</mark>](https://app.aviator.co/settings/workspace/integrations). Once a token is generated, you can reset it to invalidate your existing token and create a new one.

This token should be provided via the HTTP `Authorization` header with the value `Bearer <token>`.

## User Access Token (UAT)

A user access token provides access to API resources on behalf of a user. Users can have UATs. User access tokens can be created from the [<mark style="color:blue;">Aviator dashboard</mark>](https://app.aviator.co/settings/personal/api_token). These tokens always start with `av_uat_`.

This token should be provided via the HTTP `Authorization` header with the value `Bearer <token>`.
