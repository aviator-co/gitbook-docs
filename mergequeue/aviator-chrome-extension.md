---
description: >-
  Access Aviator's Chrome extension for MergeQueue and AttentionSet features.
  Find implementation and usecase instructions in our documentation.
icon: puzzle-piece
---

# Aviator's Chrome Extension

[<mark style="color:blue;">Aviator's Chrome Extension</mark>](https://chrome.google.com/webstore/detail/aviator-chrome-extension/inoabloekooadaolcncfmpgafkgbgnif) offers a few capabilities:

* MergeQueue - You can enqueue / dequeue your pull-request from GitHub, monitor the status and review the stack.
* AttentionSet - Track all the PRs that require your attention. On the GitHub page for the PR, you can also toggle the attention.

## Installation

Install from [<mark style="color:blue;">Chrome Web Store</mark>](https://chrome.google.com/webstore/detail/aviator-chrome-extension/inoabloekooadaolcncfmpgafkgbgnif). After the installation, open your pull-request, and you can find a log-in button instead of the GitHub merge button.

<figure><img src="../.gitbook/assets/image (4) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

Click the login button, and you'll redirected to the Aviator login page. If you are already logged in, it should automatically get back to the original PR page.

{% hint style="info" %}
The browser extension is also supported on Arc.
{% endhint %}

### Pinning the extension

Once installed, you can optionally pin the Chrome extension. This way, whenever there's a PR that require your attention, it will show you a red badge within your browser.

### Connecting with GitHub

To enable the AttentionSet in Chrome Extension, you need to connect your Aviator user with GitHub user. You can find that configuration in Settings > Personal > Integrations , or [follow this link](https://app.aviator.co/settings/personal/integrations).

## How to use

### AttentionSet

<figure><img src="../.gitbook/assets/Screenshot 2024-10-10 at 5.01.07 PM.png" alt="" width="563"><figcaption></figcaption></figure>

The extension shows you the list of PRs that require your attention. These may be PRs that require a review from you, or the PRs authored by you that are waiting for your action after a response from the reviewer.

* If you pin the extension to Chrome, it will show the number of PRs that require your attention as a badge.
* On clicking the icon, it expands a popup view that lists those PRs
* Any PR that is approved and ready to merge shows up with a green bar.
* On the PR details page, you can also view and toggle the Attention for the GitHub users from the right side menu.

<figure><img src="../.gitbook/assets/Screenshot 2024-10-10 at 9.01.43 PM.png" alt="" width="334"><figcaption></figcaption></figure>

### MergeQueue

If the repository is configured with Aviator MergeQueue, it shows a button to enqueue a pull request. This will work properly with stacked PRs as well.

<figure><img src="../.gitbook/assets/Screen Shot 2023-11-07 at 3.22.42 PM.png" alt="" width="563"><figcaption><p>Enqueue an open PR</p></figcaption></figure>

Once the PR has entered the queue, the extension will show information about the bot pull request, and a timeline of the PR's activity. If the queue is currently paused, the extension will notify the user, regardless of the PR's status.

<figure><img src="../.gitbook/assets/Screen Shot 2023-11-03 at 5.03.32 PM.png" alt="" width="563"><figcaption><p>PR in the merge queue</p></figcaption></figure>

The extension will show updated information if the PR is blocked.

<figure><img src="../.gitbook/assets/Screen Shot 2023-11-07 at 3.31.41 PM.png" alt="" width="563"><figcaption><p>Blocked PR</p></figcaption></figure>

If you need, there is an option to show the original GitHub merge button.

### Use with Aviator MergeQueue on-prem

You can open the extension option page from the extension menu.

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

You can specify your on-prem Aviator deployment URL.

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>
