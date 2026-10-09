---
description: >-
  Changelog for Aviator's browser extension. Subscribe via RSS to get notified
  of new releases.
---

# Release Notes

{% updates format="full" %}

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
