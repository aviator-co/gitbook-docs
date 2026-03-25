---
description: >-
  Changelog for Aviator on-premise releases. Subscribe via RSS to get notified
  of new releases.
hidden: true
---

# Release Notes

{% updates format="full" %}

{% update date="2026-03-23" tags="2026.03.23-1-rc1, frontend:sha256:e3992f2050b931cec346fd8164d59e1f7f6910545632c154a3e9604e65d4db08, mergeit:sha256:6728197a0a4b29963ed95d79c9fd8bcb9cbc0ef9cf3a952b564d16a445fa62cc" %}
## 2026.03.23-1-rc1

frontend: `sha256:e3992f2050b931cec346fd8164d59e1f7f6910545632c154a3e9604e65d4db08`
mergeit: `sha256:6728197a0a4b29963ed95d79c9fd8bcb9cbc0ef9cf3a952b564d16a445fa62cc`

**MergeQueue**

* Fix re-queue loop when bot PR CI times out in parallel mode
* Fix parallel mode for forked repo PRs
* Deduplicate blocked PR Slack notifications
* Fix queue processing delays caused by tasks waiting on repo lock
* More graceful handling of GitHub API errors

**Runbooks**

* Fix git clone auth for repos with private submodules
{% endupdate %}

{% update date="2026-03-18" tags="2026.03.18-4-rc1, frontend:sha256:20b9a7f478770a3bd715c02f553b20ffcf32e2ca556ed3ca5738bac1f2ae6959, mergeit:sha256:134658cae49e3dccd47fd34ef3f5b6dbc72afdcc4e759dcf2c24b8111543aa5b" %}
## 2026.03.18-4-rc1

frontend: `sha256:20b9a7f478770a3bd715c02f553b20ffcf32e2ca556ed3ca5738bac1f2ae6959`
mergeit: `sha256:134658cae49e3dccd47fd34ef3f5b6dbc72afdcc4e759dcf2c24b8111543aa5b`

{% hint style="info" %}
There is a ~10% increase in memory footprint with this release.
{% endhint %}

**MergeQueue**

* Proactive dequeueing of failing non-top PRs, gated by per-repo config. [Docs](https://docs.aviator.co/mergequeue/how-to-guides/proactive-dequeue)
* Skip-line allowlist to restrict who can skip the queue. [Docs](https://docs.aviator.co/mergequeue/reference/complete-reference-guide#other)
* Fix race condition in full queue reset operations
* Fix circular dependency between bot PRs in affected targets mode

**Runbooks**

* Trigger runbook reviews on existing non-runbook PRs
* Fix template mode
* Improve Slack oneshot unicode behavior
* Bypass GitHub auth requirement for API created sessions
* Improved sandbox performance by skipping redundant file operations on reconnection
* Add retry logic and token redaction to git clone along with improved logging
* UI improvements: collapsible long chat messages, branch copy-on-click, improved title editing, session origin in hover card, content panel scroll fix, activity log expansion fix

**FlexReview**

* Fix CODEOWNERS coverage race condition and FYI-only team display in sticky comment
* Skip webhook processing when FlexReview is not active for the repo
* Fix lock convoy from repeated webhook bursts

**Misc**

* Support for multiple API tokens with names and expiration, and separate webhook signing secret management (backward compatible)
{% endupdate %}

{% update date="2026-03-12" tags="2026.03.12-3-rc1, frontend:sha256:7e410899e24eb9878198162c9d6bdd1f1b6d9f3fcf278584614b663b8ad49712, mergeit:sha256:35e953d776856190797c62e73816b8338d03f4cd9c9010d50de2a648950120fb" %}
## 2026.03.12-3-rc1

frontend: `sha256:7e410899e24eb9878198162c9d6bdd1f1b6d9f3fcf278584614b663b8ad49712`
mergeit: `sha256:35e953d776856190797c62e73816b8338d03f4cd9c9010d50de2a648950120fb`

**MergeQueue**

* Track and recover from GitHub token authentication failures automatically

**Runbooks**

* Support uploading .txt and .md spec files from the Runbooks page
* Add API as a source in the Runbooks admin dashboard

**Releases**

* Fix non-released PRs filter incorrectly showing all historical PRs
{% endupdate %}

{% update date="2026-03-11" tags="2026.03.11-1-rc1, frontend:sha256:b0f9c06a204f3ffb5b5a91b73b385e7a64f212ae79fa3c6c3f4269615f79ef68, mergeit:sha256:a54f10902ae4701a8ecf12de9ff6001987b2ba60e69836951e25f9c2a2b73b0e" %}
## 2026.03.11-1-rc1

frontend: `sha256:b0f9c06a204f3ffb5b5a91b73b385e7a64f212ae79fa3c6c3f4269615f79ef68`
mergeit: `sha256:a54f10902ae4701a8ecf12de9ff6001987b2ba60e69836951e25f9c2a2b73b0e`

**MergeQueue**

* Fix queue getting stuck on SHA mismatch during stacked PR processing
* Fix dedup logic for concurrent cancelled GitHub jobs

**Runbooks**

* Failed steps now show "Retry" instead of "Execute Next" to prevent accidentally skipping past failures
* Fix agent accidentally editing source files instead of using the revision workflow
* UI improvements: hover cards, combined details/PR tabs, content panel loading

**FlexReview**

* Fix CODEOWNERS gapfill over-counting when multiple files share the same team

**Releases**

* Improved sync performance for non-released PRs
{% endupdate %}

{% update date="2026-03-04" tags="2026.03.04-2-rc2, frontend:sha256:2196fbcea46b5d2800e3fcf72da76ebd83eb86cdbe2e0c93a5df6937c58088e5, mergeit:sha256:d2e722caf994c2ce6564bf3f18570f33aff046fc3b28bc5b566f6a37c33bad28" %}
## 2026.03.04-2-rc2

frontend: `sha256:2196fbcea46b5d2800e3fcf72da76ebd83eb86cdbe2e0c93a5df6937c58088e5`
mergeit: `sha256:d2e722caf994c2ce6564bf3f18570f33aff046fc3b28bc5b566f6a37c33bad28`

**MergeQueue**

* Clear out all dependent PRs during dequeue

**Runbooks**

* Better error handling
* Trigger Runbooks from Linear
* Trigger Runbooks via API. [Read docs](https://docs.aviator.co/runbooks/api-reference).
{% endupdate %}

{% update date="2026-03-03" tags="2026.03.03-2-rc1, frontend:sha256:d50ed9988ec46f293082f53b1c638003f664cbe6074879c3a70db6401d037ecb, mergeit:sha256:356e2acdceef32ad6376252c65f92e6999856162de72663fd580c37192ac7dce" %}
## 2026.03.03-2-rc1

frontend: `sha256:d50ed9988ec46f293082f53b1c638003f664cbe6074879c3a70db6401d037ecb`
mergeit: `sha256:356e2acdceef32ad6376252c65f92e6999856162de72663fd580c37192ac7dce`

**AttentionSet**

* Improved time tooltips and text selection

**MergeQueue**

* Break out of rare infinite loop situation in stacked PR handling
* Pull request timeline events for labeling of a PR will now show as "Submitted to queue"
* Re-fetch data from GitHub when we have SHA mismatch during bot PR creation
* Additional logging for CI failures
* Improved queue page UI for stacked PRs that are already part of a batch

**Runbooks**

* Improved PR revision handling of comments left on blocks outside of direct PR lines

**FlexReview**

* Too many assignments guard rail improvements, sticky comments should no longer disappear, and Slack messages should not go out
{% endupdate %}

{% update date="2026-02-24" tags="2026.02.24-2-rc1, frontend:sha256:8bf36b0c85c71d1397273de9422b83950215448d9a6643e31287938d4039fedd, mergeit:sha256:786bd7cd5321e7f533d0fceb3c312a73db514bc8525ab87c7e8cf67db38ca0cf" %}
## 2026.02.24-2-rc1

frontend: `sha256:8bf36b0c85c71d1397273de9422b83950215448d9a6643e31287938d4039fedd`
mergeit: `sha256:786bd7cd5321e7f533d0fceb3c312a73db514bc8525ab87c7e8cf67db38ca0cf`

**MergeQueue**

* Better failure handling in partially tagged stacked PRs
* Track who queued and dequeued PR in PR details page
* Hide basic config editor for advanced users
* Optimize DB queries for affected targets (now tested with 350K targets)
* Fix bug with not using latest master in stacked PRs
* Increase the page count for collaborators list call

**Runbooks**

* Major improvement in user interactions with the agents
* Allow revising PRs directly from chat
* Improved UI in chat
* Fix bugs with step status change propagation to child steps
* Remove branch dropdown button
* Add org name back in repository dropdown
* Separate claude sessions for step execution, and shared for main chat
* Pre-execution script now only runs once per sandbox
* Add real-time chat processing indicator

**Releases**

* Set stuck statuses as error after 60 mins
* Add Rollback and list-deployment APIs. [Read docs](https://docs.aviator.co/releases-beta/api-reference).

**FlexReview**

* Show internal/external review selection method in GitHub sticky comment if team has separate internal/external assignment methods

**Misc**

* Remove github.com hard-coded urls everywhere
{% endupdate %}

{% update date="2026-02-09" tags="2026.02.09-1-rc2, frontend:sha256:29bd192c8905b7014d28b0ef6986f5b509f9bd1195b04eada43dc09a1b96109f, mergeit:sha256:06ac071d624e84e080e8954152579e2d0e02db27867420b86fed47990ae88503" %}
## 2026.02.09-1-rc2

frontend: `sha256:29bd192c8905b7014d28b0ef6986f5b509f9bd1195b04eada43dc09a1b96109f`
mergeit: `sha256:06ac071d624e84e080e8954152579e2d0e02db27867420b86fed47990ae88503`

**MergeQueue**

* Remove repo level lock from affected targets processing
{% endupdate %}

{% update date="2026-02-09" tags="2026.02.09-1-rc1, frontend:sha256:0da69716f0e7ae975f17d40e0b668f785dba250f19209c73d0fa564f3c03eb00, mergeit:sha256:046d53905312d097234fa6422529a3b0ca4311336c25c0aee005164b4b8e7d16" %}
## 2026.02.09-1-rc1

frontend: `sha256:0da69716f0e7ae975f17d40e0b668f785dba250f19209c73d0fa564f3c03eb00`
mergeit: `sha256:046d53905312d097234fa6422529a3b0ca4311336c25c0aee005164b4b8e7d16`

**Misc**

* Added support for updated Helm charts to split webhook and web traffic

**MergeQueue**

* Add an ability to restrict MergeQueue trigger sources. [Read the docs](https://docs.aviator.co/mergequeue/reference/complete-reference-guide#blocked-queue-sources)
* Ignore `/aviator refresh` when all products are disabled for a repo

**Runbooks**

* Better error handling for git-failures
* Ability to delete Templates
{% endupdate %}

{% update date="2026-02-05" tags="2026.02.05-2-rc1, frontend:sha256:09b70ecb1d2e59ff2dbbbaf3de768783da6ea4dba3e5929365ae221db16c0ef9, mergeit:sha256:67d1ce3f7af1adde7e21b44a545e74b59d6b7c1c1dd5e0e974c76e11cb8ebcb8" %}
## 2026.02.05-2-rc1

frontend: `sha256:09b70ecb1d2e59ff2dbbbaf3de768783da6ea4dba3e5929365ae221db16c0ef9`
mergeit: `sha256:67d1ce3f7af1adde7e21b44a545e74b59d6b7c1c1dd5e0e974c76e11cb8ebcb8`

**MergeQueue**

* Additional improvements to affected target handling to prevent database contention

**Runbooks**

* Fixed missing Slack thread context in Runbook generation
* Added ability to edit existing templates
* Configurations can now be applied at the repository level, supporting Account, Repository, and Session level configurations

**FlexReview**

* Added ability to select different reviewer settings for internal and external reviews. Current configuration is applied to both internal and external during the deploy to ensure consistent behavior
{% endupdate %}

{% update date="2026-02-03" tags="2026.02.03-1-rc1, frontend:sha256:3ef81ee7c82b76755a63a5f123733ad7d87630dd4bdbd94a8c6cc01836b581c6, mergeit:sha256:470355c95ad622b1d88a8ab4c415e3bd966f12cc8ce6919f7ca68641f98e2029" %}
## 2026.02.03-1-rc1

frontend: `sha256:3ef81ee7c82b76755a63a5f123733ad7d87630dd4bdbd94a8c6cc01836b581c6`
mergeit: `sha256:470355c95ad622b1d88a8ab4c415e3bd966f12cc8ce6919f7ca68641f98e2029`

**MergeQueue**

* Improvements around database contention with large number of affected targets
* Aviator sync improvements for stacked PRs

**Runbooks**

* Revert integration into runbook task queue
* Template publishing improvements

**Releases**

* Surface GitHub errors for build/deploy failures
{% endupdate %}

{% update date="2026-01-27" tags="2026.01.27-4-rc1, frontend:sha256:781da3cefbea1a8bf0fccdd04593c3affe8beed72ceae7bb263a7fd595dc313b, mergeit:sha256:f1bda54b829dc6808eae26fed1bee00c76cc8719c3f31797e3138cc81c97fdd4" %}
## 2026.01.27-4-rc1

frontend: `sha256:781da3cefbea1a8bf0fccdd04593c3affe8beed72ceae7bb263a7fd595dc313b`
mergeit: `sha256:f1bda54b829dc6808eae26fed1bee00c76cc8719c3f31797e3138cc81c97fdd4`

{% hint style="warning" %}
**This release has a breaking change.**

1. You will need to install **pgvector plugin** before migration.
2. This release also introduces a new webserver to support websockets. This requires setting up `USE_HYPERCORN=true` as an env variable for the web pod. Only applicable if using Runbooks.
{% endhint %}

**MergeQueue**

* Move large affected targets ingestion to background task (API now returns `202` instead of `200` for large targets list)
* Show full name over hash for deleted user in audit trail
* Stacked PRs: Exclude displaying the closed PRs in Chrome Extension
* ChangeSets: Redirect old pages to the new ones

**Runbooks**

* New feature: Revert step(s)
* Task queuing for all messages, agent executions and revisions
* Ability to delete steps
* Reduce GitHub API usage
* Fix Runbooks MCP routing
* Failure handling for Sandbox connections
* Remove claude attribution from revision commit messages
* Fix bug with saving Runbooks without steps

**Releases**

* Fix a bug with cherry-pick popup release list
* Fix ordering of stacked PRs
{% endupdate %}

{% update date="2026-01-15" tags="2026.01.15-1-rc1, frontend:sha256:28e54a08fb6bd5a730531985d5dd6d4a0c23abc66c6c190cf5166704f45550e2, mergeit:sha256:1a5de3acb3bad0aa8317a070c67d0c3fdb84f0bafe44d54f4ffcf1ea76ef7290" %}
## 2026.01.15-1-rc1

frontend: `sha256:28e54a08fb6bd5a730531985d5dd6d4a0c23abc66c6c190cf5166704f45550e2`
mergeit: `sha256:1a5de3acb3bad0aa8317a070c67d0c3fdb84f0bafe44d54f4ffcf1ea76ef7290`

**Runbooks**

* Ability to delete step from the chat session

**MergeQueue**

* Restrict config editing from dashboard when it's stored in GitHub
* Add a message for PRs blocked due to barrier PR (blocking PR)
{% endupdate %}

{% update date="2026-01-12" tags="2026.01.12-2-rc1, frontend:sha256:2df0074431f6f686a192172df2d7725616e3285e466b18400eecb21c75d646bd, mergeit:sha256:bba2906f15fa70abae8870f3dcddd8f326a595134f3f533394d545ee4af2ab75" %}
## 2026.01.12-2-rc1

frontend: `sha256:2df0074431f6f686a192172df2d7725616e3285e466b18400eecb21c75d646bd`
mergeit: `sha256:bba2906f15fa70abae8870f3dcddd8f326a595134f3f533394d545ee4af2ab75`

**Runbooks**

* Fixes around no code change behavior
* Added feature for creating template from PR
* Improved step editing behavior to prevent editing of completed steps, and better carry over progress from completed steps after editing
* Introduced the ability to revert completed steps
{% endupdate %}

{% update date="2026-01-08" tags="2026.01.08-1-rc1, frontend:sha256:0c5f9dbf53d7fb555e242860ca030a95804e26e5c7ab100218df5a1fdbef54b8, mergeit:sha256:d1174ea772b499687f04110c5d8f0651194784b49e4deabc4c27eebc7c7782fb" %}
## 2026.01.08-1-rc1

frontend: `sha256:0c5f9dbf53d7fb555e242860ca030a95804e26e5c7ab100218df5a1fdbef54b8`
mergeit: `sha256:d1174ea772b499687f04110c5d8f0651194784b49e4deabc4c27eebc7c7782fb`

**General**

* SAML config now supports comma separated whitelisted domains

**Runbooks**

* Hide container selection dropdown when SSH sandboxes are configured
* Introduce a details section in the chat session
* Update PR description for Runbook generated PRs
{% endupdate %}

{% update date="2026-01-05" tags="2026.01.05-3-rc1, frontend:sha256:9b3b4be74007c719dc00fa3ac3ff00758b9d7ee2ed6e42b0bbc90e43b768b3fe, mergeit:sha256:b00ad36a4b181adec3bfa00db81fa39eb7be5ef3fefecdc29eef585354575f10" %}
## 2026.01.05-3-rc1

frontend: `sha256:9b3b4be74007c719dc00fa3ac3ff00758b9d7ee2ed6e42b0bbc90e43b768b3fe`
mergeit: `sha256:b00ad36a4b181adec3bfa00db81fa39eb7be5ef3fefecdc29eef585354575f10`

**General**

* Archived GitHub repositories get removed from Aviator

**Runbooks**

* Retry SSH sandbox connection
* Allow claude to write to `/tmp`
* Prevent storing malformed step markdown
* Support for custom E2B templates

**Releases**

* Improved error handling of missing workflow configurations
{% endupdate %}

{% endupdates %}
