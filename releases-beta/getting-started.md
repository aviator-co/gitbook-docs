# Getting started

This document guides you to the set up for setting up [<mark style="color:blue;">Aviator Releases</mark>](https://www.aviator.co/releases) using GitHub actions.

## Prerequisite

* This guide requires using GitHub Action for your build and deploy workflows. [<mark style="color:blue;">Look up your CI / CD guide</mark>](how-to-guides/working-with-your-ci-cd/) If you are using other tool.
* <mark style="background-color:yellow;">\[For existing MergeQueue users only]</mark> If you have set up Aviator for MergeQueue before, make sure you have approved the new permission request for `Actions`, so that Aviator is able to fetch and trigger the GitHub actions for you.

### Separate Build and Deploy Steps

To use Aviator Releases, we recommend [<mark style="color:blue;">separating out the build and deploy process</mark>](concepts/two-step-delivery.md) if you run them together. This ensures a simpler and meaningful process for rollbacks. You may still use it with a single step, in which case the creation of [<mark style="color:blue;">Release</mark>](concepts/terminology.md#release) will be a no-op.

## Set up instructions

1. Set up an Aviator account, and walk through the initial onboarding to connect your GitHub repository. Select Releases as the capability.

{% hint style="info" %}
If you have trouble connecting the GitHub app, please read the [<mark style="color:blue;">troubleshooting doc</mark>](../manage/faqs/troubleshooting-github-app-connection.md).&#x20;
{% endhint %}

2. Now, click on [<mark style="color:blue;">Releases</mark>](https://app.aviator.co/releases) tab in the menu and navigate to creating your first project.
3. In this release project, create new deploy environments and specify the workflows to be triggered
   1. The dropdown for workflows should auto-populate with your GitHub actions workflows.

### GitHub Actions set up

1. Generate an Aviator API token at [https://app.aviator.co/settings/workspace/integrations](https://app.aviator.co/settings/workspace/integrations)
2. Set up environment variables and secrets for your GitHub repo at `Settings > Secrets and Variables > Actions`
   1. Add a repository secret `AVIATOR_API_TOKEN` as the API token generated above

#### Build workflow

1. While setting up Aviator Releases, we recommend duplicating your existing workflow files in GitHub for a smoother transition.
   1. NOTE: The new workflows need to be merged to your default branch in order to take effect
2. Aviator triggers GitHub using [workflow dispatch REST API](https://docs.github.com/en/actions/using-workflows/manually-running-a-workflow#running-a-workflow-using-the-rest-api). This workflow requires specifying the following params in your workflow file:

```yaml
on:
  workflow_dispatch:
    inputs:
      aviator_release_cut_id:
        description: "Database ID of release cut"
        required: false
        type: string
      aviator_release_cut_commit_hash:
        description: "Commit SHA, branch name, or tag of the HEAD to be built"
        required: false
        type: string
      aviator_release_candidate_version:
        description: "Name of the version"
        required: true
        type: string
```

1.  Add a workflow job at the beginning of the build workflow to sync the workflow run ID with Aviator. This helps Aviator track the CI action that’s running your build.

    ```yaml
    steps:
      - if: inputs.release_cut_id != ''
        name: Sync workflow run ID via Aviator API
        uses: fjogeleit/http-request-action@v1
        with:
          url: '<https://api.aviator.co/api/v1/sync-build-github-action>'
          method: 'POST'
          bearerToken: ${{ secrets.AVIATOR_API_TOKEN }}
          data: '{"release_cut_id": "${{ inputs.release_cut_id }}", "workflow_run_id": "${{ github.run_id }}"}'

      - name: Checkout the repository
        uses: actions/checkout@v4
        with:
          # if custom commit_sha is not defined, this should fall back to the head branch
          ref: "${{ inputs.commit_sha }}"
          lfs: true
          submodules: 'recursive'
    ```
2. After this you can keep your regular builds steps as is. Make sure to tag the build artifacts with the release version using `${{inputs.version}}` so that you can refer to them in the deployment step.

#### Deploy workflow

1. Similar to the Build step, duplicate the Deploy step as well and apply the following parameters in your workflow file. Note that these are different than the build params:

```yaml
on:
  workflow_dispatch:
    inputs:
      deployment_id:
        description: "Database ID of deployment"
        required: false
        type: string
      commit_sha:
        description: "Commit SHA, branch name, or tag of the HEAD"
        required: false
        type: string
      version:
        description: "Version to deploy"
        required: true
        type: string
```

1.  Add a workflow job at the beginning of the deploy workflow to sync the workflow run ID with Aviator. Note that the API URL is different from the build workflow.

    ```yaml
    steps:
      - if: inputs.deployment_id != ''
        name: Sync workflow run ID via Aviator API
        uses: fjogeleit/http-request-action@v1
        with:
          url: '<https://api.aviator.co/api/v1/sync-deploy-github-action>'
          method: 'POST'
          bearerToken: ${{ secrets.AVIATOR_API_TOKEN }}
          data: '{"deployment_id": "${{ inputs.deployment_id }}", "workflow_run_id": "${{ github.run_id }}"}'
          
    	- name: Checkout the repository
        uses: actions/checkout@v4
        with:
          # if custom commit_sha is not defined, this should fall back to the head branch
          ref: "${{ inputs.commit_sha }}"
          lfs: true
          submodules: 'recursive'
    ```
2. After this you can keep your regular deploys steps as is. Make sure to use the same build artifacts with the release version using `${{inputs.version}}` so that you can refer to them in the deployment step.

Now you should be ready to use Aviator Release Management!
