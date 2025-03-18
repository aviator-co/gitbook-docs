---
description: Guide on how to set up and manage OOO exclusions.
---

# Out of Office User Exclusion

FlexReview allows users to set out of office times for themselves and other GitHub users within the organization. This temporarily adds users to the FlexReview exclusion list so that they don't get assigned as reviewers when they're out of office.

## Adding/Removing Out of Office Events

Available in the Settings in the Personal section under Out of Office. Allows setting start date, end date, and the reason for the OOO. To switch users navigate using the search drop down in the top right.

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption><p>Out of Office Configuration</p></figcaption></figure>

### Adding via API

To integrate with external systems or bulk add out of offices, you can use our GraphQL API, documentation is available at [graphql.md](../../api/reference/graphql.md "mention").

## Admin View

For admins who need a quick insight into excluded users due to out of office events, the data can be accessed via the `Settings->Collaborators`  tab in Workspace settings. In the top right you can check the `Show Out of Office users only`  which will filter the users down to only out of office users.

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption><p>Out of office user</p></figcaption></figure>

