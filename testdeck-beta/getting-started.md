---
description: >-
  Learn how to configure TestDeck and start working with it. TestDeck allows you
  to detect the most unreliable test cases and take immediate action on them.
---

# Getting started

Aviator fetches the test results by using a custom plugin. Currently, we support CircleCI and Buildkite integrations. For both plugins, you need to specify two things: `AVIATOR_API_TOKEN` to associate the data with your account and the files associated with the test artifacts.

## CircleCI

**Step 1**: Configure the API token in the [<mark style="color:blue;">environment variables section</mark>](https://circleci.com/docs/set-environment-variable/#set-an-environment-variable-in-a-project) as:

```
AVIATOR_API_TOKEN=av_live_xxxxxxx
```

**Step 2**: Add a step after your test execution step.

```yaml
version: 2.1
orbs:
  aviator-upload-orb: aviator/aviator-upload-orb@0.0.3
jobs:
  test:
    docker:
      - image: cimg/python:3.7
    steps:
      - checkout
      - run:
          name: Run tests and upload results
          command: |
            python -m pytest -vv --junitxml="test_results/output.xml"
      - aviator-upload-orb/upload:
          assets: "test_results/*.xml"
workflows:
  test-and-upload:
    jobs:
      - test
```

Once this is done, you should be able to verify that the artifacts are getting uploaded by looking at your CircleCI. You should see something like:

```bash
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  5103  100    20  100  5083     36   9295 --:--:-- --:--:-- --:--:--  9329
{
  "status": 200
}
CircleCI received exit code 0
```

The `aviator-upload-orb` used above is available publicly at [<mark style="color:blue;">aviator-co/circleci-upload-orb</mark>](https://github.com/aviator-co/circleci-upload-orb).

## Buildkite

To install Aviator on Buildkite, please use the Buildkite plugin defined [<mark style="color:blue;">here</mark>](https://github.com/buildkite-plugins/aviator-buildkite-plugin).

**Step 1**: Configure the API token as a secret in Buildkite. You can set this in an environment hook on Buildkite agents or use your preferred secret storage. Read more about [<mark style="color:blue;">managing Buildkite secrets</mark>](https://buildkite.com/docs/pipelines/secrets#exporting-secrets-with-environment-hooks).

```
AVIATOR_API_TOKEN=av_live_xxxxxxx
```

**Step 2**: Now you can either upload the artifacts from the same step as the test run or create a separate step:

**Option A: From build step**

To upload all files from an XML folder from a build step:

```yaml
steps:
  - label: "🔨 Test"
    command: "make test"
    plugins:
      - aviator#v1.0.0:
          files: "test/junit-*.xml"
```

**Option B: Using build artifacts**

You can also use build artifacts generated in a previous step:

```yaml
steps:
  # Run tests and upload 
  - label: "🔨 Test"
    command: "make test --junit=tests-N.xml"
    artifact_paths: "tests-*.xml"

  - wait

  - label: ":plane: Aviator"command: buildkite-agent artifact download tests-*.xml
    plugins:
      - aviator#v1.0.0:
          files: "tests-*.xml"
```

## GitHub Actions

**Step 1**: Configure the API token as a [<mark style="color:blue;">secret in your repository</mark>](https://docs.github.com/en/actions/security-guides/encrypted-secrets).

**Step 2**: Add the GitHub Action as a step in your job. Make sure to specify the inputs `assets` and `aviator_api_token`. Add the conditional `if: success() || failure()` in the step to ensure that the test files are always uploaded, regardless of test failure/success.

```yaml
name: Run tests and upload results

on: [push]

jobs:
  test-and-upload:
    name: Test and Upload
    runs-on: ubuntu-latest

    steps:
    - name: Checkout
      uses: actions/checkout@v3
    - name: Set up Python 3.7
      uses: actions/setup-python@v1
      with:
        python-version: 3.7
    - name: Install dependencies and run test
      run: |
        pip install pytest
        python -m pytest -vv --junitxml="test_results/output.xml"
    - name: Upload files with github action
      if: success() || failure()
      uses: aviator-co/upload-action@v0.1.1
      with:
        assets: test_results/output.xml
        aviator_api_token: ${{ secrets.AVIATOR_API_TOKEN }}
```

The GitHub action is available publicly at [<mark style="color:blue;">aviator-co/upload-action</mark>](https://github.com/aviator-co/upload-action). You can also find it on [<mark style="color:blue;">the GitHub Marketplace</mark>](https://github.com/marketplace/actions/aviator-test-uploader).
