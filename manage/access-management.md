---
description: >-
  Find details on access management in Aviator. Learn how to manage team access,
  available user roles, Google SSO login, and SAML-based authentication config.
---

# Access Management

## **Managing team access**

Aviator offers multi-user account capabilities in all plans. You can invite all your team members to access the Aviator dashboard at no additional cost. See the [<mark style="color:blue;">Billing</mark>](faqs/billing.md) section to understand how pricing works.

To invite your team members to access the Aviator dashboard, go to the Users and Roles page, and enter their email address along with an appropriate role.

### **Users and Roles**

There are two different roles you can find in the User and Roles section.

* **ADMIN**: The admin has access to the entire Aviator product, including the ability to add more users and manage billing.
* **MAINTAINER**: A maintainer has all the permissions as a member, and can also modify the config and take queue actions such as pausing, deactivating or resetting the queue. A maintainer cannot delete a repository. A maintainer cannot review billing information, invite other members or revoke someone's access.
* **MEMBER**: A member is only allowed to view the dashboard and the queue. They are not allowed to modify the config, take actions on a queue or delete a repository. The members are not allowed to invite others or revoke someone’s access. Members also cannot view or update billing information.

## **Google SSO**

Aviator also offers capability for users to login using Google SSO. Additionally, you may whitelist your entire company domain to access your Aviator account without having to invite each of them individually. To do so, scroll to the bottom of the Users and Roles page, and activate the setting:

`Allow all users with email domain: <your_domain> to automatically login to this account.`

## **SAML authentication**

Aviator also offers SAML based authentication that can be configured for Okta or other SAML providers. We only offer SAML authentication in our enterprise plans, [<mark style="color:blue;">read more here</mark>](../mergequeue/how-to-guides/saml-configuration/).
