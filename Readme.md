<p align="center">
  <img src="Assets/app-256.png" alt="SMTP Client Pro" width="128" />
</p>

<h1 align="center">SMTP Client Pro</h1>

<p align="center">
  A Windows desktop app for composing, sending and debugging email over SMTP.<br/>
  Version 2.0 · .NET 8 · Windows Forms · MailKit
</p>

<p align="center">
  <img src="screenshots/01-main-light.png" alt="Main window (light theme)" width="49%" />
  <img src="screenshots/02-main-dark.png" alt="Main window (dark theme)" width="49%" />
</p>

---

## Contents

- [Features](#features)
- [Screenshots](#screenshots)
- [Getting started](#getting-started)
- [Using the app](#using-the-app)
- [Provider presets](#provider-presets)
- [Troubleshooting](#troubleshooting)
- [Data and security](#data-and-security)
- [Credits](#credits)

## Features

**Connection**
- Presets for Gmail, Outlook / Microsoft 365, Yahoo, iCloud, Zoho, SendGrid, Mailgun, Amazon SES and local test servers.
- Every security mode: Auto, implicit SSL/TLS (465), STARTTLS required (587), STARTTLS if available, and unencrypted. The app warns when the port and security mode don't match.
- Optional authentication, configurable timeout (5–600 s), and an "accept invalid certificates" switch for test servers.
- **Test connection** connects and authenticates without sending anything, then reports the TLS version, supported auth methods and the server's maximum message size.

**Composing**
- To, Cc, Bcc and Reply-To, separated by commas, semicolons or new lines (`alice@example.com; Bob <bob@example.com>`). Addresses are checked before sending.
- Plain-text or HTML body. HTML messages get a plain-text alternative generated for you.
- **Preview** tab that shows HTML as the recipient will see it.
- Priority (high / normal / low, written to the `Importance`, `Priority` and `X-Priority` headers) and an optional read-receipt request.
- Attachments by button or drag and drop, with a warning when they add up to more than 25 MB.
- **Export .eml** saves the composed message to a file without sending it.

**Bulk sending and personalisation**
- **Send individually** sends each To recipient a separate copy, so nobody sees the other addresses. All copies go over a single connection.
- `{{name}}`, `{{firstname}}` and `{{email}}` in the subject or body are replaced for each recipient. In HTML bodies the values are HTML-encoded.
- An optional delay between messages helps with provider rate limits.
- If one recipient fails, the rest still get sent. The app reconnects when the server drops the connection after a rejected message.

**Diagnostics**
- An activity log with the SMTP transcript (`C:` / `S:` lines). Passwords are redacted and message bodies are shown as a one-line size summary.
- Plain-English explanations for common errors such as authentication, TLS handshake, unreachable host and rejected recipients.
- Save the log to a `.txt` file, plus a progress bar and **Stop** button for long batches.

**Profiles and appearance**
- Save as many server and sender profiles as you like. Passwords are encrypted with Windows DPAPI.
- Light, dark, or follow-Windows theme, including dark title bars, scrollbars and menus.

## Screenshots

| | Light | Dark |
|---|---|---|
| Compose (Gmail preset) | ![](screenshots/03-compose-gmail-light.png) | ![](screenshots/04-compose-gmail-dark.png) |
| Send with SMTP transcript | ![](screenshots/06-send-transcript-light.png) | ![](screenshots/05-send-transcript-dark.png) |
| HTML preview | ![](screenshots/07-html-preview-light.png) | ![](screenshots/08-html-preview-dark.png) |

## Getting started

### Requirements

- Windows 10 or 11 (x64).
- The app is self-contained, so you don't need to install .NET to run it.

### Install

1. Download `SMTPClientPro-2.0-win-x64.zip` from this repository.
2. Extract it anywhere.
3. Run `SMTPClientPro.exe`. Nothing needs to be installed.

## Using the app

1. **Pick a preset** or choose *Custom* and enter the host, port and security mode yourself.
2. **Enter credentials** if the server needs them. Most consumer providers want an **app password**, not your normal password (see [Provider presets](#provider-presets)).
3. Fill in the sender's **From** name and address, plus an optional Reply-To.
4. Click **Test connection** to check the settings before you send anything.
5. **Compose** the message: recipients, subject and body, and optionally HTML, priority, a read receipt or attachments.
6. Click **▶ Send** or press **Ctrl+Enter**. Progress and the server's replies appear in the activity log.
7. Click **Save** or press **Ctrl+S** to keep these settings as a profile for next time.

### Keyboard shortcuts

| Shortcut | Action |
|---|---|
| `Ctrl+Enter` | Send |
| `Ctrl+S` | Save the current profile |
| `Esc` | Stop the current operation |
| `Delete` | Remove selected attachments |

### Personalisation tokens

These work only when **Send individually** is on. Cc and Bcc are ignored in that mode.

| Token | Replaced with | Example for `Jane Doe <jane@example.com>` |
|---|---|---|
| `{{name}}` | Display name, or the part before `@` if there is no name | `Jane Doe` |
| `{{firstname}}` | First word of the name | `Jane` |
| `{{email}}` | Email address | `jane@example.com` |

Tokens are case-insensitive and may include spaces (`{{ FirstName }}`). The preview uses the first recipient's values.

## Provider presets

| Preset | Host | Port / security | Notes |
|---|---|---|---|
| Gmail / Google Workspace | `smtp.gmail.com` | 587 STARTTLS | Needs 2-Step Verification and an [App Password](https://myaccount.google.com/apppasswords). |
| Outlook.com / Microsoft 365 | `smtp.office365.com` | 587 STARTTLS | SMTP AUTH must be enabled for the mailbox. Some tenants block basic auth. |
| Yahoo Mail | `smtp.mail.yahoo.com` | 465 SSL/TLS | Generate an app password under Yahoo Account Security. |
| iCloud Mail | `smtp.mail.me.com` | 587 STARTTLS | Use an app-specific password from appleid.apple.com. |
| Zoho Mail | `smtp.zoho.com` | 465 SSL/TLS | Paid and organisation accounts use `smtppro.zoho.com`. |
| SendGrid | `smtp.sendgrid.net` | 587 STARTTLS | The user name is literally `apikey`, and the password is your API key. |
| Mailgun | `smtp.mailgun.org` | 587 STARTTLS | Use the SMTP credentials from your domain settings. |
| Amazon SES (us-east-1) | `email-smtp.us-east-1.amazonaws.com` | 587 STARTTLS | Use SES SMTP credentials, not AWS access keys. Change the region in the host if needed. |
| Local test server | `localhost` | 25, none | For [smtp4dev](https://github.com/rnwood/smtp4dev) or [Papercut](https://github.com/ChangemakerStudios/Papercut-SMTP). Mail is captured locally and never delivered. |

## Troubleshooting

| Symptom | Likely cause and fix |
|---|---|
| **Authentication failed** | Wrong credentials, or the provider requires an app password (Gmail, Yahoo, iCloud). With Microsoft 365, SMTP AUTH may be disabled for the mailbox. |
| **TLS handshake failed** | The security mode doesn't match the port. Use SSL/TLS for 465 and STARTTLS for 587. For a test server with a self-signed certificate, tick *Accept invalid certificates*. |
| **Could not reach the server** | Wrong host or port, or a firewall or ISP is blocking it. Many ISPs block outbound port 25. |
| **Server rejected the request (5xx)** | The recipient or sender was refused, for example the From address isn't allowed for this account or the message is too large. The full reply is in the transcript. |
| **Saved password is empty** | Passwords are encrypted for the Windows user who saved them. They can't be decrypted by another user or on another PC, so enter the password again. |

Leave **Show SMTP transcript** on and use **Save...** to keep the log when reporting a problem. Passwords are already redacted in it.

## Data and security

- **Where settings are stored:** `%AppData%\SMTP Client Pro\profiles.json`. This holds profiles, the last-used profile and the theme.
- **Passwords** are encrypted with Windows DPAPI (`DataProtectionScope.CurrentUser`). They are never stored in plain text and only the same Windows account on the same machine can decrypt them.
- **Upgrading from "Simple SMTP Client":** profiles in `%AppData%\SimpleSmtpClient\profiles.json` are copied over on first run. The old file is left as it was.
- **Corrupt settings file:** if the file can't be read, it is renamed to `profiles.json.corrupt-<timestamp>` rather than overwritten, and the app starts with empty profiles.
- **Accept invalid certificates** turns off TLS certificate validation. Only use it with test servers you control. The log shows a warning whenever it is on.
- Sent messages carry the header `X-Mailer: SMTP Client Pro 2.0`.

## Credits

- **Author:** LucKY
- **Powered by:** [AI Engineering Foundry](https://aiengfoundry.com)
- **Support:** [support@aiengfoundry.com](mailto:support@aiengfoundry.com)

Copyright © 2026 LucKY, AI Engineering Foundry.
