---
description: >-
  Learn how to connect your app using a Personal Developer token instead of
  using Aviator app authentication when connecting as a GitHub app has
  limitations.
---

# Create Personal Access Tokens

There are some scenarios where connecting as a GitHub app may have certain limitations (for instance using SAML restricted authentication). In such cases, you should connect your app using a Personal Developer token instead of using Aviator app authentication.

To do so, you can create a personal developer token and [<mark style="color:blue;">set the token here</mark>](https://mergequeue.com/github/auth/dev_token). You can find instructions on how to [<mark style="color:blue;">generate a token here</mark>](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token)<mark style="color:blue;">.</mark> You may create a separate GitHub user for this purpose. If you choose to only connected Personal Access token, you may have to also setup custom webhooks to send requests to Aviator. Please contact us at [<mark style="color:blue;">howto@aviator.co</mark>](mailto:howto@aviator.co) for details.
