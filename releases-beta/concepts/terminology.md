---
description: >-
  Read the terminology for the Releases feature. The common terms are important
  to understand the Release process with Aviator and use the tool more
  effectively.
---

# Terminology

## Release Project

Release Project is the top-level entity in Aviator Releases. It refers to software that need to be built and deployed at the same time. Typically, this could be a single service, application, or library, however it could also be defined as multiple services that have to be deployed altogether. Aviator recommend designing the release projects as granular as possible for better manageability.

A Release Project can have one more [<mark style="color:blue;">Environments</mark>](terminology.md#environment), and is tied to a unique CD workflows for each of those environments. When cutting a Release, it would trigger a separate CI workflow to build the artifacts that are then deployed to specific environments using the CD workflow.

Each Release Project is tied to 1 GitHub repository, but each GitHub repository can have multiple Release Projects, each of which represent separate [<mark style="color:blue;">Changelog</mark>](terminology.md#changelog).

## Environment

Each [<mark style="color:blue;">Release Project</mark>](terminology.md#release-project) can have one or more Environments that are deployed and managed. Most common examples of Environments are `staging` and `production`, but depending on different use cases, there may be additional environments you can configure, e.g. `canary`, `dogfood`, `pre-production`, `sandbox`, `development`.

There is no limit to the number of Environments for a Release Project. Each Release Project defines its own Environments, and there is no correlation of same Environments across different release projects.

## Changelog

Changelog is one of the core utilities of Aviator Releases. The Release dashboard records of all the changes as pull requests in a linear history. This way it’s easy to understand which code changes (PRs) are deployed with which [<mark style="color:blue;">Release</mark>](terminology.md#release) and on which [<mark style="color:blue;">Environments</mark>](terminology.md#environment). Each Release project has a it’s own

Changelog makes it easier for any engineer to track and understand when their changes were deployed. The Changelog also represents the [<mark style="color:blue;">Cherry-picks</mark>](terminology.md#cherry-pick), and tie them to the corresponding Release and [Deployments](terminology.md#deployment).

Changelogs help users and developers track the progression of the software, understand what has changed from one version to another, and ensure compatibility with other systems or components.

## Release

A Release is a snapshot of a commit SHA from the primary branch that is tested and eventually deployed to various environments. A release contains one or more [<mark style="color:blue;">Release Candidates</mark>](terminology.md#release-candidate). Each Release is also associated with a unique [<mark style="color:blue;">Release Version</mark>](terminology.md#release-version). A Release is also associated with a git commit SHA, but this association is mutable. That means, as new Release Candidates are created the the git commit SHA for a Release can change.

## Release Candidate

Each Release has one or more Release Candidates represented by `rc1`, `rc2` and so on. A Release Candidate is always tied a unique git commit SHA and is immutable. There is no limit in the number of Release candidates as Release can have. Typically there is only one valid Release Candidate for any Release at a given time. The default Release Candidate for a Release is represented by `rc1`.

## Release version

Each [<mark style="color:blue;">Release</mark>](terminology.md#release) is represented by a unique version that typically follows a pattern. Aviator Releases supports some common release version patterns such as [CalVer](https://calver.org/) and [SemVer](https://semver.org/). But you can also define and maintain your own release version formats.

Aviator does not automatically create Git-tags for the release.

## Release Candidate version

Release Candidate version is a full string that uniquely identifies both a Release and a corresponding Release Candidate version. A Release Candidate version is created by appending `.rc<number>` to a [<mark style="color:blue;">Release version</mark>](terminology.md#release-version). For instance, if the Release version is: `2024.06.20` then the first Release Candidate version will be `2024.06.20.rc1`, and second one will be `2024.06.20.rc2` and so on.

## Build

To effectively use Aviator Releases we recommend [<mark style="color:blue;">separating out the build and deployment process</mark>](two-step-delivery.md). A build process is where the artifacts are built. A common example of this would be building one or more docker images. To manage the Build process via Aviator Releases, you will have to configure Aviator to trigger your build CI workflow. When triggering this pipeline, Aviator passes the [<mark style="color:blue;">Release Candidate version</mark>](terminology.md#release-candidate-version) as a parameter along with the git commit SHA to your build CI workflow. This helps uniquely tie the build to a given Release Candidate to generate the artifacts.

## Deployment

Deployment is an action of taking existing build artifacts and pushing those to a specific Environment. To manage the Deployments via Aviator Releases, you will have to configure Aviator to trigger your a deployment CI or CD workflow. Similar to the Build step, the Deployment step also passes the Release Candidate version and the git commit SHA to your configured workflow.

## Cherry-pick

Cherry-pick allows you to apply any arbitrary commit on top of an existing Release Candidate to create a new Release Candidate. This is also sometimes called hotfixing. This can be useful in scenarios where you don’t want to cut a new release because there are too many commits being introduced since the last release was cut and you want to surgically hotfix a bug. Find more details on the [<mark style="color:blue;">Cherry-picks</mark>](cherry-picks.md) page.
