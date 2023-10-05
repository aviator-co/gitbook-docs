# Introduction

![](.gitbook/assets/A\_Illustration.svg)

Aviator is a fast, customizable workflow automation tool for developers to manage their build, test, merge and deploy processes. Aviator’s scalable framework helps teams avoid broken builds and unreliable tests, and manage pull requests using smart heuristics.

There are 4 key components of Aviator:

<table data-card-size="large" data-column-title-hidden data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><h2>MergeQueue</h2></td><td>An automated queue to manage the merging workflow for your GitHub pull requests to help protect important branches from broken builds. Built for scale, Aviator’s merge queue is highly customizable. It can merge over 10,000 changes in a day.</td><td></td><td><a href="mergequeue/">mergequeue</a></td></tr><tr><td><h2>TestDeck</h2></td><td>A platform to automatically detect and manage unreliable tests in your CI infrastructure.</td><td><p></p><p>Dive deep into what’s causing test failures. Get test case level insights. Identify unreliable tests, quarantine or rerun them.</p></td><td><a href="testdeck-beta/">testdeck-beta</a></td></tr><tr><td><h2>Stacked PRs CLI</h2></td><td>A command line tool that helps developers manage cross-PR dependencies. This tool also automates syncing and merging of stacked PRs. Useful when your team wants to promote a culture of smaller, incremental PRs instead of large changes, or when your workflows involve keeping multiple, dependent PRs in sync.</td><td></td><td><a href="aviator-cli/">Aviator CLI</a></td></tr><tr><td><h2>Pilot automated actions</h2></td><td>Build custom workflows on top of GitHub and CI systems using automated actions. Trigger nightly CI builds, define an emergency merge workflow, auto-assign reviewers, or send Slack reminders.</td><td></td><td><a href="pilot-automated-actions.md">pilot-automated-actions.md</a></td></tr></tbody></table>

## How Aviator works

Aviator connects as a GitHub app that can be installed on any repository. The CLI can be installed using HomeBrew and configured using your GitHub personal access token. Aviator also has CI plugins that collect test results to monitor test reliability.
