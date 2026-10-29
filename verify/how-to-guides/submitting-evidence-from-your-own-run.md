# Submitting evidence from your own run

Some changes can't be exercised in a preview: there is no ephemeral environment for the repo yet, the change needs data or hardware a preview doesn't have, or you already ran it on your machine and watched it work. An **externally verified review** lets you hand Verify what you saw. You run the change yourself, describe the scenarios you exercised and attach the screenshots and responses each step produced, and Verify judges the acceptance criteria against that evidence the same way it judges evidence it collected itself.

On such a review Aviator never builds a preview and never drives the app. It still picks the invariants that apply, decides which criteria your scenarios can answer and which need a code scan, runs the code scan, and produces a verdict per criterion.

For the alternative, where Aviator runs the scenarios against an environment, see [Creating a preview](creating-a-preview.md) and [Registering an external preview](registering-an-external-preview.md).

**Time:** ~15 minutes the first time, a few minutes after

**Prerequisites:**

* The repo connected to Aviator — see [Connect a repository](connect-a-repository.md)
* The `aviator` CLI, signed in — see [Setting up your machine](../setting-up-your-machine.md)

### Step 1: Create the review with an external evidence source

Create the review as usual, adding `--evidence-source external`:

```bash
aviator verify \
  --repo myorg/myrepo \
  --working-branch add-retry-button \
  --intent "Let an operator retry a rejected webhook delivery from the delivery log" \
  --criteria-file criteria.txt \
  --evidence-source external
```

An existing review can be switched with `aviator edit r/123 --evidence-source external` while no verification run is in progress, and back with `--evidence-source aviator`.

The review page shows the evidence source under session settings, and the review's scenarios can't be regenerated: they are whatever you submit.

### Step 2: Run the change and capture what it did

Run the branch wherever you normally would. As you exercise it, save what each step showed. Verify accepts these kinds of evidence:

| Type              | What it is                                            | File format             |
| ----------------- | ----------------------------------------------------- | ----------------------- |
| `screenshot`      | What the screen showed                                | `.png`, `.jpg`, `.webp` |
| `dom_snapshot`    | The page's markup at that moment                      | `.html`                 |
| `console_log`     | Browser or process console output                     | `.txt`, `.log`          |
| `api_response`    | The body of an HTTP response                          | `.json`, `.txt`         |
| `network_request` | A request and its response, as captured               | `.json`, `.txt`         |
| `client_storage`  | Cookies, local storage or session storage at the time | `.json`, `.txt`         |

A file is at most 10 MB. Give every file a label saying what it shows: the label is what the reviewer and the evaluator read first.

### Step 3: Write the manifest

The manifest lists the scenarios you ran. A scenario has a one-line summary and ordered steps; a step may attach evidence files. Paths are relative to the manifest file.

```yaml
# evidence.yaml
commit: 4f2c9e1a7b3d5c6e8f9a0b1c2d3e4f5a6b7c8d9e   # required, the full SHA you ran
scenarios:
  - summary: Retry a rejected delivery from the delivery log
    criteria:              # optional, see below
      - stable_key: ac-7f3a
    steps:
      - text: Open the delivery log filtered to rejected deliveries
      - text: Click Retry on the rejected push delivery
        evidence:
          - type: screenshot
            file: shots/rejected-before-retry.png
            label: Delivery log filtered to rejected deliveries before the retry
      - text: Confirm the retried delivery appears as routed
        evidence:
          - type: screenshot
            file: shots/retried-routed.png
            label: The retried push delivery routed with 200
          - type: api_response
            file: api/retry-response.json
            label: POST /deliveries/812/retry returned 200
```

Every scenario needs evidence on at least one step. A manifest may hold up to 10 scenarios, 50 steps per scenario and 10 files per step. JSON works the same way as YAML.

**Pinning criteria.** `criteria` names the acceptance criteria a scenario covers, by the `stable_key` that `aviator show r/123` prints, or by `baseline_invariant_id` for an invariant. Pinning is optional. When a run starts, Verify maps every criterion you did not pin onto the submitted scenarios whose evidence can show it, and routes the rest to code scan. Pin a criterion when you want to be sure which scenario answers it.

### Step 4: Submit

```bash
aviator evidence submit r/123 evidence.yaml
```

The CLI validates the manifest, creates the scenarios, uploads each file to storage and confirms the uploads. It prints the scenarios it created and how many files landed; with `--json` it prints a single object with the scenario, scenario run and evidence ids.

Submitting replaces whatever was submitted for the review before. To change a scenario or add evidence, edit the manifest and submit it again.

The evidence is bound to the commit you ran: the full 40-character SHA in the manifest's `commit`, or `--commit` on the command line, which takes precedence. One of them is required. A run judges from submitted evidence only when the pull request's head is that commit. After you push a new commit, run the change again and submit again.

### Step 5: Start a run

Submitting starts nothing. Start a run when you're ready:

```bash
aviator verify r/123
```

The usual triggers also start one: marking the PR ready for review, approving it, or queueing it. Every run on an externally verified review goes straight to judging; there is no collector phase and no preview to wait for.

If no complete submission exists for the PR's head commit, the run still completes, judging every criterion from code, and says so on the review page.

### Reading the result

`aviator results r/123 --json` reads the verdicts as usual. `aviator scenarios r/123` lists the scenarios the run judged; a submitted one shows who submitted it and for which commit instead of a tool call count. On the review page, a submitted scenario shows the same in place of the tool call count, and its screenshots open in the evidence viewer.

### What Verify trusts

Verify takes submitted evidence as a faithful record of a run of that commit. It records who submitted it and for which commit, and shows both on the review page, so a reviewer can see what the verdict rests on. It does not check that the files came from the commit they claim.
