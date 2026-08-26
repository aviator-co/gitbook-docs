# Setting up your machine

Your team uses Aviator Verify, which checks each pull request against the intent and acceptance criteria behind it. Your coding agent captures those as you work, once you've done the setup below.

## 1. Sign in to Aviator and connect your GitHub account

Sign in at [app.aviator.co](https://app.aviator.co). You need an invite to your company's workspace, so ask whoever set up Verify if you don't have one.

Then open [Settings → Personal → Integrations](https://app.aviator.co/settings/personal/integrations). The GitHub card should read **Connected**. If it reads **Not connected**, click **Connect with GitHub**. Without this, the pull requests you open never link back to Verify.

<figure><img src="../.gitbook/assets/verify-github-integration.png" alt="The GitHub Integration card in Aviator settings, badged Not connected, with a Connect with GitHub button on the right"><figcaption><p>Click <strong>Connect with GitHub</strong> when the card reads <strong>Not connected</strong></p></figcaption></figure>

## 2. Install and set up the Aviator CLI

```bash
brew trust aviator-co/tap
brew install aviator-co/tap/aviator
aviator login
```

`brew trust` is needed on Homebrew 6 and later, and Linux users can grab the `.deb` or `.rpm` from the [releases page](https://github.com/aviator-co/aviator-cli/releases). `aviator login` opens your browser and stores the session in your OS keychain.

## 3. Install the Aviator skill

Your agent runs `/verify-submit` to capture the intent and criteria. It ships separately from the CLI, in the [Aviator agent plugins](https://github.com/aviator-co/agent-plugins).

In Claude Code:

```
/plugin marketplace add aviator-co/agent-plugins
/plugin install aviator@aviator-plugins
```

Codex users install the `verify-submit` skill from the same repository, then run `/hooks` and trust the Aviator hook. Nothing fires until you do.

<figure><img src="../.gitbook/assets/verify-codex-hooks-trust.png" alt="The Codex hooks screen warning that 3 hooks need review before they can run, listing PreToolUse, PostToolUse and SessionStart with counts, and offering to press t to trust all"><figcaption><p>Codex lists the three Aviator hooks as needing review until you trust them</p></figcaption></figure>

## Check that it works

Start a **fresh** agent session in the repo, make a small change, and let it open a pull request. The agent should run `/verify-submit`, print a session URL, and put a `Runbook:` line at the top of the PR body. The Aviator Verify check then appears on the pull request.

<figure><img src="../.gitbook/assets/verify-check-on-pull-request.png" alt="The aviator/verify check on a pull request, reading Successful in 2m, Verification passed"><figcaption><p>The <code>aviator/verify</code> check on the pull request</p></figcaption></figure>

## When something doesn't fire

| Symptom | Cause |
| --- | --- |
| The agent never mentions Verify | The session predates the hooks. Start a new one. |
| Nothing fires in Codex | The hook isn't trusted yet. Run `/hooks` in Codex. |
| `/verify-submit` isn't a command | The Aviator skill isn't installed on this machine. |
| The agent says the CLI isn't installed | It isn't on `PATH` here. Install it, step 2 above. |
| The agent says no credentials were found | Run `aviator login`. |
| Submitting fails with "Repository not found" | You're signed in to a personal account rather than your company's workspace. |
| The session exists but the pull request never links to it | Your GitHub account isn't connected. Check Settings → Personal → Integrations. |
| The pull request has no Verify check | The `Runbook:` line is missing from the body, or the repo isn't connected. See [Fixing verification failures](how-to-guides/fixing-verification-failures.md). |

## See also

* [Your first verification](your-first-spec.md), a hands-on run through the whole loop
* [Writing effective acceptance criteria](how-to-guides/writing-effective-acceptance-criteria.md)
* [Fixing verification failures](how-to-guides/fixing-verification-failures.md)
