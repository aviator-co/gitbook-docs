---
description: >-
  Learn how to create custom sections in Aviator Inbox to organize pull requests
  with personalized filters based on authors, labels, repositories, and more.
---

# Custom sections

Custom sections let you create personalized views of pull requests in your Inbox. Each custom section is defined by a set of filters and appears alongside the predefined sections in your Inbox sidebar.

## Creating a custom section

To create a custom section:

1. Click the **+ Add section** button in the Inbox sidebar.
2. Give your section a name (e.g., "Frontend team PRs" or "Failing CI").
3. Configure the filters for the section (see [Available filters](#available-filters) below).
4. Optionally enable the **badge count** to show the number of matching PRs in the sidebar.
5. Save the section.

<!-- TODO: Add screenshot of the custom section editor -->

## Available filters

Custom sections support the following filters:

| Filter | Description |
|--------|-------------|
| **Authors** | Show only PRs authored by specific GitHub users. |
| **Labels** | Show only PRs with specific GitHub labels. |
| **Repositories** | Limit the section to PRs from specific repositories. |
| **Draft status** | Filter by whether the PR is a draft or not. |
| **PR status** | Filter by pull request status (e.g., pending, queued). |
| **CI status** | Filter by CI status — passing or failing. |

You can combine multiple filters to create precise views. For example, you could create a section that shows all non-draft PRs from a specific repository with failing CI.

## Managing custom sections

### Reordering

Custom sections can be reordered in the sidebar by dragging them to the desired position.

### Editing

To edit a custom section, click the edit icon next to the section name in the sidebar. You can update the section name, filters, and badge count setting.

### Deleting

To delete a custom section, click the edit icon and then select **Delete**. This action cannot be undone.

## Badge counts

Each custom section can optionally show a badge count in the sidebar, displaying the number of PRs matching the section's filters. This is useful for at-a-glance visibility into sections that need attention.

To toggle the badge count, edit the section and check or uncheck the **Show badge count** option.

## Examples

Here are some useful custom section configurations:

- **Urgent reviews**: Filter by a "urgent" or "priority" label to surface high-priority PRs.
- **Team PRs**: Filter by authors who are members of your team.
- **Failing CI**: Filter by CI status "failing" to quickly find PRs that need fixing.
- **Release PRs**: Filter by a "release" label to track release-related changes.
