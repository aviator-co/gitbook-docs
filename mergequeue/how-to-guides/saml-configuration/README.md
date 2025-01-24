---
description: >-
  Follow our guide to configure SAML configuration. The documentation explains
  the process of setting up the single sign-on supported across Aviator's
  accounts.
---

# Set Up SAML Configuration

All of Aviator’s cloud accounts support Google SSO based login. Self-hosted Aviator deployments support Google, Okta, Active Directory, or other SAML SSO providers.

## SAML 2.0

Aviator supports SAML 2.0 based authentication. To request SAML authentication for your account, please contact [<mark style="color:blue;">howto@aviator.co</mark>](mailto:howto@aviator.co). See instructions below for Okta. If you have any other identity provider, please contact us for instructions.

### Note for onprem users

Please replace _app.aviator.co_ with with **aviator.yourdomain.com** in the instructions below.

### Okta setup

1. Sign into Okta as an administrator.
2. Go to Admin Dashboard > Applications > **Add Application**. If you don't see that option, you might need to switch to the **Classic UI**, using the drop-down in the upper left.
3. Click **Create New App** and choose **SAML 2.0** as the Sign on method.
4. Enter **General Settings** for the application:
   * App name: **Aviator**
   * **App logo** (optional). You can download the application logo for the application, you can download one from [<mark style="color:blue;">here</mark>](https://api.aviator.co/static/img/aviator_long.png).
5. Log into Aviator and go to SAML configuration page: [<mark style="color:blue;">https://app.aviator.co/saml/okta/configure</mark>](https://app.aviator.co/saml/okta/configure)
6. Copy the unique **Single Sign on url**, of format: [<mark style="color:blue;">**https://app.aviator.co/saml/sso/**</mark>](https://aviator.yourdomain.com/saml/sso/)**\<sso-key>**

![identity provider setup](<../../../.gitbook/assets/Screen Shot 2023-02-09 at 10.25.39 AM.png>)

8\. Enter SAML Settings, including:

* Single sign on URL: enter the URL you copied in Step 6
* Audience URI: `mergequeue`
* Default Relay state: \<leave empty>
* Name ID format: `EmailAddress`
* Application username: `Email`

9\. Enter the attribute statements, which will be used to map attributes between Okta and Aviator. Please note that these values are case-sensitive.

![](<../../../.gitbook/assets/Screen Shot 2022-05-10 at 12.07.08 PM.png>)

10\. Click **Next**. Then, set Okta support parameters for the application. Recommended settings:

* I’m an Okta customer adding an internal app
* This is an internal app that we have created.

11\. Click **Finish**. On the next screen, click the **Sign On** tab and go to SAML Signing Certificates and select SHA-2 Actions dropdown. Select View IdP metadata.

<figure><img src="../../../.gitbook/assets/Screen Shot 2023-02-14 at 9.36.27 PM.png" alt=""><figcaption></figcaption></figure>

12\. Copy the url that it opens, this is your **Metadata URL**. It should typically end with: `/sso/saml/metadata`

13\. Go to the **Assignments** tab, and assign the app to the appropriate groups / users to access.

14\. Go back to the SAML configuration page and update the following properties: [<mark style="color:blue;">https://app.aviator.co/saml/okta/configure</mark>](https://app.aviator.co/saml/okta/configure)

* **Metadata url**: Paste the Metadata URL copied from step 11
* **Email domains to allow**: enter your company email domain, e.g. [<mark style="color:blue;">**example.com**</mark>](http://example.com)
* **Click Save and Activate**

This should enable the Okta configuration for your organization. Please verify this by logging out and logging in directly from Okta portal.

Notes:

* This is idp initiated authentication, so you can login in directly from the Okta portal.
* It’s also recommended to post an announcement for your users to explain how the migration will work.

Contact: [<mark style="color:blue;">support@aviator.co</mark>](mailto:support@aviator.co) if you have any issues with the setup.
