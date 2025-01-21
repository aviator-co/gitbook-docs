---
icon: rocket-launch
description: >-
  Documentation for Aviator Releases, a dashboard that helps engineering teams
  manage deployments across environments in a central dashboard.
---

# Releases

## Introduction

Aviator Releases enables product engineers to manage their own releases. It is a unified dashboard that helps teams confidently manage releases, deployments, cherrypicks and rollbacks across multiple services and environments. It integrates with existing CI/CD systems (Jenkins, Buildkite, CircleCI, GitHub Actions, ArgoCD, Spinnaker, FluxCD) and GitHub.



{% embed url="https://www.youtube.com/watch?v=gumS1LMu53s" %}
Aviator Release Demo
{% endembed %}

## Why Releases

Deploying software in most organizations is still a brittle process, often managed by a small team of engineers. There is rarely a defined process for handling deployment failures. Rollbacks and cherry-pick processes can be challenging to execute, and are prone to human errors.

Product engineers often struggle to track changes—whether they have been deployed to production, rolled back, or pending verification in staging.

Modern deployment tools like Spinnaker and ArgoCD mitigate some concerns around automatic rollbacks and early failure detection. However, those tools can be hard for product engineers to use confidently, and lack the understanding of the code changes that are being deployed.

Releases helps democratize the process of deployments and release management for all engineers, while providing the right set of access control and guardrails to avoid human errors.

## Who is Releases for

Aviator Releases can be relevant at any stage of the company. It’s most useful when you have a frequent but manual deploy process, where you are looking to incorporate better standardization. it can help provide more visibility and control to the product engineering tams, and to improve the engineering practices internally.
