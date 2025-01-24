---
description: >-
  Learn how to set up Google SSO for on-premise installation. Find guidelines on
  creating OAuth authorization credentials in the Google developer console.
---

# Google SSO Login

To setup Google SSO for on-premise installation, you will need to create OAuth authorization credentials in the Google developer console to identify the application to Google's OAuth 2.0 server.

1. Go to the [<mark style="color:blue;">Developer Credentials page</mark>](https://console.developers.google.com/apis/credentials).
2. Click **Create credentials > OAuth client ID**.
3. Select the **Web application** application type.
4. Name your OAuth 2.0 client and add the Javascript origins and redirect urls replacing the following with your domain.

<figure><img src="../../.gitbook/assets/Screen Shot 2022-05-10 at 11.28.34 AM.png" alt=""><figcaption></figcaption></figure>

5. Add the Google Client ID and Client Secret that is on this page to your docker `.env`  file.

```shell
GOOGLE_CLIENT_ID={YOUR_GOOGLE_CLIENT_ID}
GOOGLE_CLIENT_SECRET={YOUR_GOOGLE_CLIENT_SECRET}
```

Restart the server, and Google SSO should work.
