---
description: >-
  Learn how to troubleshoot the GitHub app connection with step-by-step
  instructions. Configured when setting up Aviator FlexReview, MergeQueue, or
  Releases.
---

# Troubleshooting GitHub app connection

GitHub app connection is critical to configure when setting up [<mark style="color:blue;">FlexReview</mark>](../../flexreview/), [<mark style="color:blue;">MergeQueue</mark>](../../mergequeue/) or [<mark style="color:blue;">Releases</mark>](../../releases-beta/). If your GitHub organization requires app approvals by GitHub admin, and you are not one of those admins, the connection request goes through an approval.

## Pending approval workflow

GitHub app connection via request-approval workflow is not supported natively within GitHub. We [have an open ticket](https://d4kz6r04.na1.hs-sales-engage.com/Ctc/RL+23284/d4KZ6r04/Jl22-6qcW7lCdLW6lZ3lwW2F3yGt5RplFGW5Pp_4N5Mcs-CN3Kp9-XhFLzlW5h3H2z59s6RqVzBj2d5QQ4DzW78ZFW17x43gZW8lZgdD4C2ZfXW8cRxJv4_MjLhW2tBjpX4QtdDSW7nrJx07JGwr2W45Smtt10nY1cW8gtbYf5C0qm_W26WT3r30C-LYW5v3jg58cnDyYW3HmG1z6QwN-2W6wtxRt8cC5WNN6Lkm2lZ4dcyW3l3PPZ8gcqx9V82THp8dHltXN5wtW0Bh4mqyW1dKXsl5lVSpyW1mWFJL6DB6n7W57ldLY8R8Cv_W5RStxT6SfP5nf2MfCgn04) requesting GitHub to add that support.

Follow the following steps to handle the pending approval workflow:

### 1. Discuss with the GitHub admin

Ensure that your GitHub admin team is aware of this integration. You may have to ask them to approve the request manually. Typically they would receive an email with the link to review and approve the request. They may sometimes need to understand the usage of the app, you can share our [<mark style="color:blue;">GitHub App permissions guidelines</mark>](../github-app-permissions.md) for their reference.

An easier path to getting this connection work seamlessly is [<mark style="color:blue;">inviting them to the Aviator account</mark>](../access-management.md). If they log into the Aviator account before going through the approval workflow, the app connection is automatically set up.

### 2. Get installation ID

So, if you have gotten approval from admin, but they were not already logged in to Aviator, the connection would not have established automatically.

In that case, go to GitHub organization settings:

```

https://github.com/organizations/<org_name>/settings/profile
```

&#x20;and click on "GitHub apps". Then click "Configure" next to the Aviator app.

<figure><img src="../../.gitbook/assets/Screenshot 2024-08-25 at 1.29.41 PM.png" alt=""><figcaption><p>Configure button on the GitHub apps page</p></figcaption></figure>

On this page, you will see the `installation_id` in url. The url will be of the format and `installation_id` is the number in the end:

```
https://github.com/organizations/<org_name>/settings/installations/<installation_id>
```

### 3. Complete the installation

Log onto Aviator and then go to this this url (replace the installation\_id with the one from the URL above):

```
https://app.aviator.co/api/setup/complete?setup_action=install&installation_id=<installation_id>
```

If the app is still not connected, please email us at [howto@aviator.co](mailto:howto@aviator.co).<br>
