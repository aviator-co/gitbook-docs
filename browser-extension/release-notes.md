---
description: >-
  Changelog for Aviator's browser extension. Subscribe via RSS to get notified
  of new releases.
---

# Release Notes

{% updates format="full" %}

{% update date="2026-11-09" %}
## 2026.11.09-2-rc1

**MergeQueue**

* The repository's pull request list shows whether each pull request is queueing, queued, blocked or merged by Aviator
* The Aviator merge panel works on GitHub's new pull request Overview page

**Verify**

* The Verify tab shows the pull request's decisions, grouped into open and settled, with their comments. [Read the docs](https://docs.aviator.co/verify/how-to-guides/working-with-decisions)
* Authors who haven't signed in to the Aviator CLI get a prompt to copy into their coding agent, which sets up the CLI and walks through the results. [Read the docs](https://docs.aviator.co/verify/setting-up-your-machine)
{% endupdate %}

{% update date="2026-11-02" %}
## 2026.11.02-2-rc1

**MergeQueue**

* Queued pull requests show their Aviator status on GitHub's merge button again instead of a red X
* The state label in the pull request header now shows Queueing before Queued

**Verify**

* The Verify tab shows each criterion's screenshots, which open in a viewer you can step through with the arrow keys
* Any failing acceptance criterion can now be waived with a reason from the Verify tab, not only invariants
* Waived criteria show who waived them and at which commit
{% endupdate %}

{% update date="2026-10-19" %}
## 2026.10.19-1-rc1

**Verify**

* The Verify tab appears again on pull requests after GitHub's latest change to its pull request header
* The Verify tab has its own URL, so the back button, reloads and shared links open it
{% endupdate %}

{% update date="2026-10-08" %}
## 2026.10.08-1-rc1

**Verify**

* Criteria that come from an invariant show the invariant's title instead of its full rule text
{% endupdate %}

{% update date="2026-09-24" %}
## 2026.09.24-1-rc1

**MergeQueue**

* Blocked pull requests can be queued from the merge box without removing the blocked label first
* Pull requests merged outside Aviator now show as Merged via GitHub, with the branch they merged into
* On a base branch Aviator doesn't manage, the merge box warns which branch GitHub's merge button will merge into
* Copying a stack as markdown keeps its links intact when pull request titles contain brackets
{% endupdate %}

{% update date="2026-08-03" %}
## 2026.08.03-1-rc1

**MergeQueue**

* The queue button on a stacked pull request shows how many pull requests it will queue
* The stack list shows READY for pull requests waiting only on the rest of their stack
* The State badge reads Merged on every pull request tab when Aviator merged it
* The merge box links to the pull request's page in Aviator
* On a base branch Aviator doesn't manage, the merge box explains why instead of offering a queue button
* The merge commit link opens on your GitHub Enterprise host
* The stack navigation bar lines up with the diff column instead of covering GitHub's file tree

**Verify**

* New Verify tab on the pull request page to rerun, waive or remove criteria without leaving GitHub. [Read the docs](https://docs.aviator.co/mergequeue/aviator-chrome-extension#verify).

**Inbox**

* Approved pull requests stay out of the Returned section and the toolbar badge count

**General**

* Now available for Firefox. [Get it from Firefox Add-ons](https://addons.mozilla.org/firefox/addon/aviator/).
{% endupdate %}
{% endupdates %}
