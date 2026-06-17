---
description: Building blocks for shipping AI-generated code quickly and reliably.
---

# Introduction

![](.gitbook/assets/A_Illustration.svg)

AI writes code faster than teams can review, merge, and release it. The bottleneck moved — from typing the change to everything that happens after.

Aviator is a suite of building blocks for that *everything-after*: capturing intent, verifying behavior, queuing safe merges, coordinating releases, and routing reviewer attention to where it matters. Some pieces are open and free, designed to drop in beside what you already use. The rest are the power tools we build for teams pushing thousands of PRs a day.

## Getting started — free tools

Three entry points. Adopt them in isolation; combine when you're ready.

<table data-card-size="large" data-column-title-hidden data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><h4>Inbox</h4></td><td>One feed for every change that needs your attention — across every repo. Stops the polling-Slack-and-GitHub-notifications habit and replaces it with a single ranked queue.</td><td></td><td><a href="inbox/">inbox</a></td></tr><tr><td><h4>Stacked PRs CLI</h4></td><td>An open-source CLI for managing stacked PRs natively in GitHub. Smaller, focused reviews; cleaner history; no extra hosting.</td><td></td><td><a href="aviator-cli/">aviator-cli</a></td></tr><tr><td><h4>Team Reviews</h4></td><td>Team-level config for review assignment, with response-time SLOs and automated escalations. The first place to start when review latency is your bottleneck.</td><td></td><td><a href="flexreview/">flexreview</a></td></tr></tbody></table>

## Power tools

Four products that compound on each other once you're shipping at scale.

<table data-card-size="large" data-column-title-hidden data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><h4>Verify</h4></td><td>Capture the intent for every change and verify the running code against it before merge. Deterministic guardrails for AI-generated code — catches the slop that diff review misses.</td><td></td><td><a href="verify/">verify</a></td></tr><tr><td><h4>MergeQueue</h4></td><td>The most popular and most scalable merge queue in the industry. In production at Figma, DoorDash, Notion, Meta, and others — tens of thousands of PRs through the queue every day.</td><td></td><td><a href="mergequeue/">mergequeue</a></td></tr><tr><td><h4>Releases</h4></td><td>One dashboard for deployments, rollbacks, and cherry-picks across every environment. Replaces the spreadsheet-and-Slack release ritual with a single source of truth.</td><td></td><td><a href="releases-beta/">releases-beta</a></td></tr><tr><td><h4>Runbooks</h4></td><td>Multiplayer AI coding with standardized playbooks. Your team's repeatable work — refactors, migrations, upgrades — turned into agentic runbooks anyone can launch.</td><td></td><td><a href="runbooks/">runbooks</a></td></tr></tbody></table>
