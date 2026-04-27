---
description: >-
  Configure SCIM 2.0 provisioning so your identity provider (e.g., Okta) can
  automatically create, update, and deactivate users in Aviator.
---

# SCIM Provisioning

Aviator supports the [SCIM 2.0](https://datatracker.ietf.org/doc/html/rfc7644) protocol for automated user lifecycle management. Once enabled, your identity provider pushes user creates, attribute updates, and deactivations to Aviator over the SCIM API — no manual user management is required.

### Prerequisites

* You must have **SAML SSO already configured** for your account. See [Set Up SAML Configuration](./). SCIM only manages provisioning; sign-in still flows through SAML.
* You must be an **admin** on the Aviator account to enable SCIM and generate the bearer token.

### Note for onprem users

Please replace _app.aviator.co_ with **aviator.yourdomain.com** in the instructions below.

## Enable SCIM in Aviator

1. Sign into Aviator as an admin.
2. Go to **Settings → Workspace → Integrations**: [<mark style="color:blue;">https://app.aviator.co/settings/workspace/integrations</mark>](https://app.aviator.co/settings/workspace/integrations).
3. Scroll to the **SCIM Provisioning** section.
4. Click **Enable SCIM**, confirm in the modal, and Aviator will generate a bearer token.
5. **Copy the bearer token immediately** — it is shown only once and cannot be retrieved again. Store it in your identity provider's secrets manager.
6. Note the **SCIM Endpoint URL** displayed on the page. It will look like:\
   [<mark style="color:blue;">**https://app.aviator.co/api/scim/v2**</mark>](https://app.aviator.co/api/scim/v2)

If you ever lose the bearer token, click **Disable SCIM**, then **Enable SCIM** again to generate a new one. Existing users will not be affected.

## Okta SCIM setup

1. Sign into Okta as an administrator.
2. Open the same Okta application you created for SAML SSO (see [Set Up SAML Configuration](./)).
3. Go to the **General** tab and click **Edit** next to **App Settings**. Check **Enable SCIM provisioning**, then **Save**.
4. A new **Provisioning** tab appears. Open it and select **Integration** in the left sidebar.
5. Fill in the SCIM connector fields:
   * **SCIM connector base URL**: the SCIM Endpoint URL from Aviator, e.g. `https://app.aviator.co/api/scim/v2`
   * **Unique identifier field for users**: `userName`
   * **Supported provisioning actions**: check **Push New Users**, **Push Profile Updates**, and **Push Groups** (groups are not yet supported but it's safe to leave checked).
   * **Authentication Mode**: `HTTP Header`
   * **HTTP Header → Authorization**: `Bearer <paste the bearer token from Aviator>`
6. Click **Test Connector Configuration**. Okta should report success on `Test Users endpoint` and `Test Groups endpoint`.
7. Click **Save**.

## Enable provisioning actions

In the same **Provisioning** tab, switch to **To App** in the left sidebar, then click **Edit**:

* **Create Users** → check **Enable**
* **Update User Attributes** → check **Enable**
* **Deactivate Users** → check **Enable**

Click **Save**. Without these toggles, Okta will silently skip the corresponding SCIM calls, and you'll see errors like _"Matching user not found"_ when assigning new users.

## Assign users

1. Go to the **Assignments** tab of the Okta app.
2. Assign individual users or groups. Okta will immediately call Aviator's SCIM API to create them.
3. Verify in Aviator under **Settings → Workspace → Members** that the new users appear.

When a user is unassigned in Okta (or deactivated in their Okta profile), Okta sends a SCIM `PATCH` to Aviator that soft-deletes the user. Their email is obfuscated so the address can be reused later if they're re-provisioned.

## Supported attributes

Aviator's SCIM endpoint reads the following attributes from incoming user payloads:

| SCIM attribute | Aviator field | Notes |
| -------------- | ------------- | ----- |
| `userName` | `email` | Lowercased on store. This is the matching attribute. |
| `name.givenName` + `name.familyName` | `full_name` | Joined as `"<given> <family>"`. Preferred over `name.formatted`, since Okta often sends a stale `formatted` value when the user edits their first/last name. |
| `name.formatted` | `full_name` | Used as a fallback when `givenName`/`familyName` are both empty. |
| `active` | `deleted` | `active=false` soft-deletes the user. |

All other SCIM attributes (phone numbers, addresses, locale, etc.) are accepted but ignored.

## Endpoints

For reference, Aviator implements the following SCIM 2.0 endpoints under `/api/scim/v2`:

| Method | Path | Purpose |
| ------ | ---- | ------- |
| `GET` | `/Users` | List / filter users (supports `userName eq "..."`) |
| `POST` | `/Users` | Create user |
| `GET` | `/Users/{id}` | Retrieve user |
| `PUT` | `/Users/{id}` | Replace user |
| `PATCH` | `/Users/{id}` | Partial update (used for deactivation) |
| `GET` | `/ServiceProviderConfig` | Discovery (no auth required) |

All authenticated endpoints expect `Authorization: Bearer <token>` and return SCIM-compliant JSON.

Contact: [<mark style="color:blue;">support@aviator.co</mark>](mailto:support@aviator.co) if you have any issues with the setup.
