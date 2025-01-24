---
description: >-
  Follow our guide for instructions on creating a release project with examples.
  A Release Project is a top-level entity you must build and deploy
  simultaneously.
---

# Creating a Release Project

A [<mark style="color:blue;">Release Project</mark>](../concepts/terminology.md#release-project) is a top-level entity that needs to be built an deployed simultaneously.

<figure><img src="../../.gitbook/assets/Screenshot 2024-07-07 at 7.56.08 AM.png" alt=""><figcaption></figcaption></figure>

## Choosing the granularity

Typically, even in a large micro-services architecture many services may be deployed together. In such cases, all of those will be tied to a single Release Project. If each service is deployed separately, then each of them should be it’s own Release project. Each Release Project can have multiple environments that it deploys to, in which case all of those environments should share the same Release Project.

Aviator recommends making release projects as granular as possible for better manageability.

## Release Configuration

Other than the GitHub Repository name, all the Release Project properties are mutable.

### Project Name

A readable string representing the name of the project. Do not use any special characters other than `-`, `_` or a space. The project name is only used for reference purposes and is mutable. Each project name within the Aviator workspace should have a unique name.

### Repository

Represents the primary GitHub repository containing the project’s source code. This is the repository that Aviator uses to generate the Changelog. When using the GitOps based tool such as ArgoCD or FluxCD, we recommend choosing your application repository as the primary repository.

Each Release Project is tied to 1 GitHub repository, but each GitHub repository can have multiple Release Projects, each of which represent separate Changelog.

### Git tag patterns

<mark style="background-color:orange;">\[Coming soon]</mark>

Aviator autogenerates and publishes [Git-tags](https://git-scm.com/book/en/v2/Git-Basics-Tagging) to track the Release Candidates. Aviator supports 2 Git tag patterns: [Semver](https://semver.org/) and [Calver](https://calver.org/) to automatically generate versions for new releases. These versions are not enforced and can be overridden.

This is an optional field, can be left blank.

### File code path

A list of glob patterns that presents the file paths associated with the Release Project. This file follows similar patterns to [gitignore](https://git-scm.com/docs/gitignore#_pattern_format). This is a great way to filter out the changes associated with

This is an optional field, can be left blank. When left blank, all code changes in the repository are associated with the Release Project. The field is mutable, but it does not update the Changelog associated with the past release versions.

## Build Configuration

If you are using a [<mark style="color:blue;">two-step delivery</mark>](../concepts/two-step-delivery.md) workflow, the build step should be configured here. A more detailed explanation of each supported CI is explained in the [<mark style="color:blue;">CI specific pages</mark>](working-with-your-ci-cd/).

### Not configured

If you want to skip the build step entirely (one-step delivery workflow), you can choose the build step to be “Not configured”. You can also choose this step if you decide to build an entirely API-driven workflow.

### GitHub Actions

<figure><img src="../../.gitbook/assets/Screenshot 2024-07-07 at 8.36.43 AM.png" alt=""><figcaption><p>Build step with GitHub</p></figcaption></figure>

Since GitHub authorization already provides Aviator access to the GitHub workflows, integrating GitHub actions is fairly straight-forward. Simply select the GitHub workflow that should be triggered for creating a build.

If you do not see your workflow in the dropdown, click on “Fetch workflows” to async fetch the workflow names.

Note: Please read the full [<mark style="color:blue;">GitHub Actions guide</mark>](working-with-your-ci-cd/github-actions-workflow.md) to configure the GitHub workflow parameters and callbacks to ensure your build and deploy workflows can be tracked in Aviator.

### Buildkite

<figure><img src="../../.gitbook/assets/Screenshot 2024-07-07 at 9.16.53 AM.png" alt=""><figcaption><p>Build step with Buildkite</p></figcaption></figure>

Aviator uses Buildkite [API access tokens](https://buildkite.com/docs/apis/managing-api-tokens) to trigger the Buildkite workflows. Please read the [<mark style="color:blue;">Buildkite setup guide</mark>](working-with-your-ci-cd/buildkite-workflow.md) to understand how to configure the access tokens with the right permissions.

On the Release Project page, configure the Organization slug and Pipeline slug to be able to trigger and track the Buildkite pipelines. Please note that these slugs are case sensitive. No callbacks are necessary to track the workflows in Aviator with Buildkite.

## Editing a Release Project

Once a Release Project is created, you can edit all the project configuration other than the GitHub Repository name. None of the changes should impact your existing released versions and changelog. To edit the Release Project, go to the Releases main dashboard, and click on the Gear icon next to the Release Project name.

<figure><img src="../../.gitbook/assets/Screenshot 2024-07-07 at 9.21.49 AM.png" alt=""><figcaption></figcaption></figure>

Once the Release Project is created, next step is to [<mark style="color:blue;">set up the environments</mark>](configuring-environments.md).
