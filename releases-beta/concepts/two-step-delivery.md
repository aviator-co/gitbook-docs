# Two-step delivery

In many software companies with cloud based infrastructure, releases and deployments are used interchangeably. When using [trunk based development](https://trunkbaseddevelopment.com/) and Aviator Releases, we recommend splitting the delivery in two steps.

{% embed url="https://youtu.be/yEuALidHHhY" %}

In a two-step delivery workflow we create a build, and then deploy that build to an environment, or more appropriately:

* **Cutting a release**: Cutting a release is an act of snapshotting your mainline (trunk) by picking a commit SHA, thereby creating a release candidate. In this process, you build the artifacts that will be then used to deploy. The advantage is to build this once and deploy the same artifacts to all environments ensuring that you are testing the same images in all environments. After cutting a release, it may be deployed to various pre-production environments (sandbox, staging, etc) for validation. During this process, any critical bugs identified may also be fixed and cherry-picked to create additional release candidates.
* **Deployment**: Once a release candidate is validated in pre-production environments, it is then deployed to production using the same build artifacts.

That means, we build once during the process of cutting a release, and then those build artifacts are then deployed to all environments.

## Advantages

There are a few advantages of separating builds and deploys:

* **Isolation of Concerns**: Build step focuses on code compilation and testing; deploy focuses on moving artifacts to environments.
* **Consistency**: Ensures the same artifact is used across environments reducing environmental discrepancies.
* **Faster Feedback Loops**: Quick feedback on build issues without waiting for deployment.
* **Simplified Rollbacks**: Easier to revert by redeploying previous artifacts.
* **Scalability**: Build and deploy processes can be scaled and optimized independently.
* **Better Resource Utilization**: Optimize resources separately for build and deployment.

### Aviator Releases

Using separate steps helps Aviator streamline the process of creating one-click rollbacks, and ensure that the same builds can verified in an environment before those are released to production.

## Continuous Delivery (CD)

The modern CD tools such as [Argo, Flux and Spinnaker](https://www.aviator.co/blog/comparing-flux-cd-argo-cd-and-spinnaker/) enable teams to manage continuous delivery, where each commit is deployed to production as soon as it’s pushed the mainline. This is possible with a comprehensive and reliable test infrastructure when manual verification or intervention is not needed.

In the system with Continuous Delivery, splitting release and deployments steps are less important, since manual rollbacks and cherrypicks are rare. For such cases, you can configure Aviator release and deployment in a single step.
