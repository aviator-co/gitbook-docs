---
description: >-
  Use our guide to set up GitHub Actions build and deploy to manage releases in
  Aviator. Instructions on authentication, building and deploying workflows.
---

# GitHub Actions workflow

Use this guide to configure GitHub actions build and deploy steps to manage releases in Aviator. To use Aviator Releases, we recommend have separate steps for the [<mark style="color:blue;">build and deploy workflows</mark>](../../concepts/two-step-delivery.md). You may still use it as a single step, in which case the creation of Release (the build part) will be a no-op.

## Authentication

1. Generate an Aviator API token at [https://app.aviator.co/settings/workspace/integrations](https://app.aviator.co/settings/workspace/integrations)
2. Set up environment variables and secrets for your GitHub repo at `Settings > Secrets and Variables > Actions`
   1. Add a repository secret `AVIATOR_API_TOKEN` as the API token generated above

## Build workflow

### Option A - Using Two-step workflow

1. When getting started with Aviator Releases, we recommend duplicating your existing workflow files in GitHub.
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
      - if: inputs.aviator_release_cut_id != ''
        name: Sync workflow run ID via Aviator API
        uses: fjogeleit/http-request-action@v1
        with:
          url: 'https://api.aviator.co/api/v1/sync-build-github-action'
          method: 'POST'
          bearerToken: ${{ secrets.AVIATOR_API_TOKEN }}
          data: '{"release_cut_id": "${{ inputs.aviator_release_cut_id }}", "workflow_run_id": "${{ github.run_id }}"}'

      - name: Checkout the repository
        uses: actions/checkout@v4
        with:
          # if custom commit_sha is not defined, this should fall back to the head branch
          ref: "${{ inputs.aviator_release_cut_commit_hash }}"
          lfs: true
          submodules: 'recursive'
    ```
2. After this you can keep your regular builds steps as is. Make sure to tag the build artifacts with the release version using `${{inputs.aviator_release_candidate_version}}` so that you can refer to them in the deployment step.

### Option B - Skipping build step

If you want to skip the Build step entirely, select “Not Configured” in the Build step:

<figure><img src="../../../.gitbook/assets/Screenshot 2024-07-08 at 8.59.22 AM.png" alt=""><figcaption><p>Skip Build step</p></figcaption></figure>

In this configuration mode, as soon as a Release is cut, the build is marked as completed. Then you can set up deployment as a separate step.

## Deploy workflow

1. Similar to the Build step, duplicate the Deploy step as well and apply the following parameters in your workflow file. Note that these are different than the build params:

```yaml
on:
  workflow_dispatch:
    inputs:
      aviator_deployment_id:
        description: "Database ID of deployment"
        required: false
        type: string
      aviator_release_cut_commit_hash:
        description: "Commit SHA, branch name, or tag of the HEAD"
        required: false
        type: string
      aviator_release_candidate_version:
        description: "Version to deploy"
        required: true
        type: string
```

1.  Add a workflow job at the beginning of the deploy workflow to sync the workflow run ID with Aviator. Note that the API URL is different from the build workflow.

    ```yaml
    steps:
      - if: inputs.aviator_deployment_id != ''
        name: Sync workflow run ID via Aviator API
        uses: fjogeleit/http-request-action@v1
        with:
          url: 'https://api.aviator.co/api/v1/sync-deploy-github-action'
          method: 'POST'
          bearerToken: ${{ secrets.AVIATOR_API_TOKEN }}
          data: '{"deployment_id": "${{ inputs.aviator_deployment_id }}", "workflow_run_id": "${{ github.run_id }}"}'
          
    	- name: Checkout the repository
        uses: actions/checkout@v4
        with:
          # if custom commit_sha is not defined, this should fall back to the head branch
          ref: "${{ inputs.aviator_release_cut_commit_hash }}"
          lfs: true
          submodules: 'recursive'
    ```
2. After this you can keep your regular deploys steps as is. Make sure to use the same build artifacts with the release version using `${{inputs.aviator_release_candidate_version}}` so that you can refer to them in the deployment step.

Now you should be ready to use Aviator Release Management!
