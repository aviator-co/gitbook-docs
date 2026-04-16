---
description: >-
  Configure the Aviator Chrome Extension for enterprise deployments using
  managed storage policies.
icon: building
---

# Enterprise Configuration

IT administrators can pre-configure the Aviator browser extension for their organization using Chrome's managed storage (enterprise policy). This is useful for on-prem deployments or organizations with custom SSO login pages.

## Extension Details

* **Chrome Web Store**: [Aviator Chrome Extension](https://chromewebstore.google.com/detail/aviator-chrome-extension/inoabloekooadaolcncfmpgafkgbgnif)
* **Extension ID**: `inoabloekooadaolcncfmpgafkgbgnif`

You will need the extension ID when configuring policies below.

## Configurable Properties

| Property          | Description                                                                                                                                                                              |
| ----------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `aviatorAppBaseURL` | Base URL of the Aviator web app (e.g. `https://aviator.example.com`). Users can still override this from the extension options page. Defaults to `https://app.aviator.co`.              |
| `aviatorLoginURL`   | URL to direct users to when they need to log in (e.g. a custom SSO page). Only configurable via enterprise policy, not from the options page. When unset, defaults to `{aviatorAppBaseURL}/auth/login`. |

## Deploying via Google Admin Console

If your organization uses Google Workspace:

1. Go to **Google Admin Console** > **Devices** > **Chrome** > **Apps & extensions**.
2. Find the Aviator extension by ID: `inoabloekooadaolcncfmpgafkgbgnif`, or add it.
3. Under **Policy for extensions**, add the following JSON:

```json
{
  "aviatorAppBaseURL": {
    "Value": "https://aviator.example.com"
  },
  "aviatorLoginURL": {
    "Value": "https://aviator.example.com/sso/login"
  }
}
```

4. Save and wait for the policy to propagate to managed Chrome profiles.

## Deploying via MDM (macOS)

For macOS deployments using MDM solutions (Jamf, Kandji, Mosyle, etc.), create a configuration profile with the payload type `com.google.Chrome.extensions.inoabloekooadaolcncfmpgafkgbgnif`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
  "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>PayloadContent</key>
  <array>
    <dict>
      <key>PayloadType</key>
      <string>com.google.Chrome.extensions.inoabloekooadaolcncfmpgafkgbgnif</string>
      <key>PayloadVersion</key>
      <integer>1</integer>
      <key>PayloadIdentifier</key>
      <string>com.example.aviator.chromeext</string>
      <key>PayloadUUID</key>
      <string>GENERATE-A-UUID-HERE</string>
      <key>PayloadDisplayName</key>
      <string>Aviator Chrome Extension Config</string>
      <key>aviatorAppBaseURL</key>
      <string>https://aviator.example.com</string>
      <key>aviatorLoginURL</key>
      <string>https://aviator.example.com/sso/login</string>
    </dict>
  </array>
  <key>PayloadDisplayName</key>
  <string>Aviator Chrome Extension Policy</string>
  <key>PayloadIdentifier</key>
  <string>com.example.aviator.chromeext.policy</string>
  <key>PayloadType</key>
  <string>Configuration</string>
  <key>PayloadUUID</key>
  <string>GENERATE-ANOTHER-UUID-HERE</string>
  <key>PayloadVersion</key>
  <integer>1</integer>
</dict>
</plist>
```

Replace the `PayloadUUID` values with unique UUIDs (generate with `uuidgen` on macOS). Upload this profile through your MDM solution and assign it to the relevant device groups.

## Deploying via Group Policy (Windows)

For Windows deployments using Group Policy or registry:

1. Open **Registry Editor** and navigate to:

```
HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\3rdparty\extensions\inoabloekooadaolcncfmpgafkgbgnif\policy
```

2. Create string values:
   * `aviatorAppBaseURL` = `https://aviator.example.com`
   * `aviatorLoginURL` = `https://aviator.example.com/sso/login`

Or deploy via Group Policy ADMX templates using Chrome's enterprise bundle.

## Force-Installing and Pinning the Extension

You can force-install the extension for all users so they don't need to install it manually.

### Google Admin Console

1. Go to **Devices** > **Chrome** > **Apps & extensions** > **Users & browsers**.
2. Add the extension by ID: `inoabloekooadaolcncfmpgafkgbgnif`.
3. Set **Installation policy** to **Force install** or **Force install + pin to toolbar**.

### Group Policy / Registry (Windows)

Add the extension to the `ExtensionInstallForcelist` policy:

```
HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\ExtensionInstallForcelist
```

Value: `inoabloekooadaolcncfmpgafkgbgnif;https://clients2.google.com/service/update2/crx`

### MDM (macOS)

Add to the `ExtensionInstallForcelist` array in your Chrome policy profile:

```xml
<key>ExtensionInstallForcelist</key>
<array>
  <string>inoabloekooadaolcncfmpgafkgbgnif;https://clients2.google.com/service/update2/crx</string>
</array>
```

## Testing Locally (macOS)

Chrome on macOS only reads extension managed storage from MDM-delivered configuration profiles. JSON policy files and manually placed plist files do **not** work for extension managed storage.

To test locally, install a `.mobileconfig` profile:

1. Create a file named `aviator-chrome-ext-test.mobileconfig` with the XML content from the [MDM section above](#deploying-via-mdm-macos), replacing the URLs with your test values (e.g. `http://localhost:8000`).

2. Double-click the file. macOS will prompt you to review and install it in **System Settings** > **General** > **Device Management**.

3. Restart Chrome completely (quit and reopen).

4. Verify the policy is active by opening the extension's service worker console (`chrome://extensions` > Inspect service worker) and running:

```js
chrome.storage.managed.get(null, console.log)
```

5. To remove the test profile, go to **System Settings** > **General** > **Device Management** and delete it.

{% hint style="warning" %}
On macOS, JSON policy files in `/Library/Google/Chrome/policies/managed/` and plist files in `/Library/Managed Preferences/` do **not** work for extension managed storage. Only `.mobileconfig` profiles or real MDM delivery will work.
{% endhint %}
