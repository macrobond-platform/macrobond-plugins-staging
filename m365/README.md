# Macrobond for Microsoft 365 Copilot

The Macrobond assistant as a **Microsoft 365 Copilot declarative agent**. Ask Copilot for
economic and financial data in plain language and it searches the Macrobond database and
renders the result as a Macrobond-styled chart or table, without leaving the chat.

The agent is a thin wrapper around the Macrobond MCP server — no data or server code is
bundled in the package.

## Table of contents

- [Requirements](#requirements)
- [Install](#install)
  - [1. Download the app package](#1-download-the-app-package)
  - [2. Upload it to your tenant](#2-upload-it-to-your-tenant)
  - [3. Connect your Macrobond account](#3-connect-your-macrobond-account)
- [Try it](#try-it)
- [Connecting the agent to Macrobond in your own tenant](#connecting-the-agent-to-macrobond-in-your-own-tenant)
- [License](#license)

## Requirements

- A [Microsoft 365 Copilot license](https://learn.microsoft.com/microsoft-365-copilot/extensibility/prerequisites#prerequisites)
- Permission to upload custom apps in your Microsoft 365 tenant — this is normally a
  Teams administrator, and it may be switched off by policy
- An active **Macrobond AI Data Feed** subscription, and a Macrobond account to sign in with

## Install

### 1. Download the app package

**[Macrobond AI Data Feed - M365 Copilot Agent](https://github.com/macrobond-platform/macrobond-plugins/releases/latest/download/Macrobond-AI-Data-Feed-M365-Copilot-Agent.zip)** *(recommended)*

That link always serves the current version — bookmark it. Do not unzip the file;
Microsoft expects the ZIP as-is.

To pin a version or roll back, every release carries a
`Macrobond-AI-Data-Feed-M365-Copilot-Agent-V<version>.zip` of its own —
[browse the releases](https://github.com/macrobond-platform/macrobond-plugins/releases).
Its version always matches the release tag and the Claude plugin published alongside it.

To check what you are installing, browse [appPackage/](appPackage/) — the exact files the
ZIP is built from. `manifest.json` carries the version and publisher, and `ai-plugin.json`
shows which Macrobond server it talks to and which tools it can call.

### 2. Upload it to your tenant

Either route works; the first makes the agent available to everyone, the second only to you.

**Tenant-wide (Teams admin center):** *Teams apps → Manage apps → Actions → Upload new app*,
then select the ZIP and approve it. Full walkthrough:
[Upload custom apps](https://learn.microsoft.com/microsoftteams/teams-custom-app-policies-and-settings).

**Just for yourself:** open the [Microsoft 365 app](https://www.microsoft365.com/chat) or
Teams, go to *Apps → Manage your apps → Upload an app → Upload a custom app*, and select
the ZIP. This requires custom-app upload to be enabled for your account.

### 3. Connect your Macrobond account

Open Copilot, pick **Macrobond** from the agent list, and ask it something. The first time
it fetches data, Copilot prompts you to sign in — use your Macrobond credentials. The
connection is remembered afterwards.

## Try it

Ask the agent:

- *"Give me a chart of Sweden GDP"*
- *"Compare US and euro area core inflation over the last ten years"*
- *"Show me a table of German unemployment, monthly, since 2020"*
- *"What was US GDP growth as first reported, versus the current figure?"*

It resolves what you asked for to concrete Macrobond series, then renders a chart or table.
For a single number it will just answer in text.

## Connecting the agent to Macrobond in your own tenant

The package references a Macrobond OAuth configuration by ID. If sign-in fails after
upload — typically an error about the plugin's authentication not being found — that
configuration does not exist in your tenant and an administrator has to register it once:

1. Open the [Teams Developer Portal](https://dev.teams.microsoft.com/) and find the
   uploaded Macrobond app.
2. Under the app's **Plugins / API access** section, register an OAuth client with:
   - **Client ID** `macrobond_mcp_search_retrieval` (public client, PKCE, no secret)
   - **Authorization URL** `https://apiauth.macrobondfinancial.com/mbauth/connect/authorize`
   - **Token URL** `https://apiauth.macrobondfinancial.com/mbauth/connect/token`
   - **Scopes** `offline_access macrobond_web_api.search_retrieval_mcp macrobond_web_api.read_mb macrobond_web_api.search_mb`
3. Copy the resulting configuration ID into `ai-plugin.json` inside the package
   (`runtimes[0].auth.reference_id`), re-zip, and re-upload.

If you would rather not do this yourself, contact
[support@macrobond.com](mailto:support@macrobond.com).

## License

See [LICENCE](../LICENCE).
