---
description: >-
  Learn how to use the Aviator's developer collaboration suite to manage build,
  test, and merge deploy processes.
---

# Introduction

![](.gitbook/assets/A_Illustration.svg)

Aviator is a fast, customizable workflow automation tool for developers to manage their build, test, merge and deploy processes. Aviator’s scalable framework helps teams avoid broken builds and unreliable tests, and improve code review process using smart heuristics.

There are 4 key components of Aviator:

<table data-card-size="large" data-column-title-hidden data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><h4>MergeQueue</h4></td><td>An automated queue to manage the merging workflow for your GitHub pull requests to help protect important branches from broken builds. Built for scale, Aviator’s merge queue is highly customizable. It can merge over 10,000 changes in a day.</td><td></td><td><a href="mergequeue/">mergequeue</a></td></tr><tr><td><h4>FlexReview</h4></td><td>Introduce flexibility to your code review process by understanding the nuances of every code change and every reviewer. Instead of defining a static <code>CODEOWNERS</code> file, it analyses the history of code review patterns to suggest reviewers.<br>Additionally teams can set response time SLOs and configure automated actions.</td><td></td><td></td></tr><tr><td><h4>Stacked PRs CLI</h4></td><td>A command line tool that helps developers manage cross-PR dependencies. This tool also automates syncing and merging of stacked PRs. Useful when your team wants to promote a culture of smaller, incremental PRs instead of large changes, or when your workflows involve keeping multiple, dependent PRs in sync.</td><td></td><td><a href="aviator-cli/">aviator-cli</a></td></tr><tr><td><h4>Releases</h4></td><td>A unified dashboard to manage releasing all services across all environments.</td><td>Eliminate human errors with automated deployments, cherry-picks, rollbacks and scheduled runs.</td><td><a href="releases-beta/">releases-beta</a></td></tr></tbody></table>

## How Aviator works

Aviator connects as a GitHub app that can be installed on any repository. The CLI can be installed using HomeBrew and configured using your GitHub personal access token. Aviator also has a Chrome Extension that gives live status updates within the GitHub UI.
