---
description: >-
  Learn how to set up SAML configuration with Microsoft Active Directory. We
  provide step-by-step instructions and samples to take you through the process.
---

# Microsoft Active Directory

### Note for onprem users

Please replace _app.aviator.co_ with with **aviator.yourdomain.com** in the instructions below.

### Azure Active Directory setup

1. Sign in to Azure portal using one of the following roles: Global Administrator, or Application Administrator.
2. Go to Azure Active Directory > Manage > Enterprise applications. Click **New application**.
3. Select Create your own application.
4. For name enter **Aviator**, and select Integrate any other application you don't find in the gallery (Non-gallery). Click **Create**.
5. (Optional): Go to properties and update the Aviator logo. Download the original from [<mark style="color:blue;">here</mark>](https://api.aviator.co/static/img/aviator_icon.png).
6. Log into Aviator and go to SAML configuration page: [<mark style="color:blue;">https://app.aviator.co/saml/okta/configure</mark>](https://app.aviator.co/saml/okta/configure)
7. Copy the unique Single Sign on url, of format: [<mark style="color:blue;">https://app.aviator.co/saml/sso/</mark>](https://app.aviator.co/saml/sso/)**\<sso-key>**
8. In the Azure portal, after creating the app, go to the app overview and click Single sign-on.
9. Select SAML.
10. In the Basic SAML configuration, enter Identifier (Entity ID) as **mergequeue**.
11. In the Reply URL, enter the URL you copied on step 6. Click Save.

<figure><img src="../../../.gitbook/assets/Screen Shot 2023-04-01 at 5.20.51 PM.png" alt=""><figcaption></figcaption></figure>

12. Under Attributes & Claims, add the following new claims and save. You can leave the other ones as is.
    1. Name: FirstName, Source attribute: user.givenname
    2. Name: LastName, Source attribute: user.surname
    3. Name: Email, Source attribute: user.primaryauthoritativeemail

<figure><img src="../../../.gitbook/assets/Screen Shot 2023-04-01 at 5.20.17 PM.png" alt=""><figcaption><p>Azure AD Attributes &#x26; Claims</p></figcaption></figure>

13. Copy the **App Federation Metadata Url** from SAML certificates and paste that in Aviator’s SAML configuration page **Metadata url**: [<mark style="color:blue;">https://app.aviator.co/saml/okta/configure</mark>](https://app.aviator.co/saml/okta/configure)
14. Enter the domain that you use for Active Directory, click **Save and Activate**. Now you should be able to login to the Aviator app using AD from your [<mark style="color:blue;">Application dashboard</mark>](https://myapplications.microsoft.com/#optIn).
