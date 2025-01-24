---
description: >-
  Check out deployment strategies that limit the impact in case of a failure
  during deployment. Dogfood, Canary, and Rollout explained with usage
  guidelines.
---

# Dogfood, Canary and Rollout

These deployment strategies that limit the impact in case of a failure during deployment.

## Dogfood

Dogfooding is a strategy to deploy the service to a cluster that is only serving internal traffic. This is a common practice for the products and services that can be tested easily within the company. For instance, if you have a business messaging app, it’s easy to test such an app by dogfooding it internally for communication. If the software serves a very specific type of user base such as a brick and mortar store, or a dentist, dogfooding may not be applicable.

## Canary

Canary is a process of deploying the application or service to a small segment of the users to ensure any failures can be quickly diagnosed and resolved without impacting the entire user base. If an issue is identified after Canary deployment, the deployment may be flagged as bad and rolled back. This can be performed via automated or automatic rollbacks. Once the Canary deployment is successful , a full production deployment may be performed automatically after some time or triggered manually.

## Rollouts

Rollouts process is somewhat similar to Canary deployment, where the application is first deployed to a small segment of the users. Unlike Canary, rollouts are performed as a percentage of the traffic that is gradually rolled out to all user base if no issue are identified. If an issue is identified, the rollouts are stopped and an automatic or automated rollback can be performed back to the last successful release.

## Applying these strategies with Aviator Releases

Each of the above deployment strategies can be configured using separate Environments within a single Release project. These environments are then deployed to those specific clusters depending on the configuration and can then be monitored for any defects before a production environment is deployed.

This provides a clear picture of all the environments and their current deployed versions for a simpler release orchestration.

{% hint style="info" %}
Note: Currently Aviator does not support gradual rollouts as a single environment with parameters. These can be configured as separate environments as a step function rollout.
{% endhint %}
