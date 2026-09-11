# macrobond-plugin

Plugin that provides the `/macrobond` skill for Claude Code, GitHub Copilot CLI, and Cursor, plus the MCP configuration each client needs to reach the server. Used together with `macrobond-mcp` — the skill provides the instructions and context for the AI agent, while the MCP server (deployed remotely) provides the tools it calls. The plugin ships an MCP config that connects the client to the remote server; the server itself is not bundled.

## Table of contents

- [Install from marketplace](#install-from-marketplace)
  - [Requirements](#requirements)
  - [Claude Code](#claude-code)
  - [GitHub Copilot CLI](#github-copilot-cli)
- [Install from ZIP](#install-from-zip)
  - [Requirements](#requirements-1)
  - [Claude Code](#claude-code-1)
  - [GitHub Copilot CLI](#github-copilot-cli-1)
- [Install in Cursor](#install-in-cursor)
- [Microsoft 365 Copilot](#microsoft-365-copilot)
  - [Requirements](#requirements-2)
  - [Tenant-wide](#tenant-wide)
  - [Just for yourself](#just-for-yourself)
- [License](#license)

## Install from marketplace

### Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code/getting-started) or [GitHub Copilot CLI](https://docs.github.com/en/copilot/github-copilot-in-the-cli/about-github-copilot-in-the-cli)
- [Git](https://git-scm.com/downloads)
- Internet access to GitHub

### Claude Code

```
/plugin marketplace add macrobond-platform/macrobond-plugins
/plugin install macrobond@macrobond-plugins
```

### GitHub Copilot CLI

```
/plugin marketplace add macrobond-platform/macrobond-plugins
/plugin install macrobond@macrobond-plugins
```

## Install from ZIP

To install the plugin from a ZIP file, download the ZIP, unzip it to a folder of your choice, and follow the steps below.

- [Latest release](https://github.com/macrobond-platform/macrobond-plugins/releases/latest) *(recommended)*
- [All releases](https://github.com/macrobond-platform/macrobond-plugins/releases)

### Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code/getting-started) or [GitHub Copilot CLI](https://docs.github.com/en/copilot/github-copilot-in-the-cli/about-github-copilot-in-the-cli)

### Claude Code

```
/plugin marketplace add <path-to-extracted-folder>
/plugin install macrobond@macrobond-plugins
```

### GitHub Copilot CLI

```
/plugin marketplace add <path-to-extracted-folder>
/plugin install macrobond@macrobond-plugins
```

## Install in Cursor

This repo is also a Cursor plugin marketplace, so Cursor installs it the same way as Claude
Code / Copilot (requires Cursor 2.5+ for Plugins; team import needs 2.6+).

- **Requirements:** [Cursor](https://cursor.com/) and internet access.
- In Cursor, go to **Dashboard → Settings → Plugins**, click **Import** under Team
  Marketplaces, and paste this repository's URL
  (`https://github.com/macrobond-platform/macrobond-plugins`).
- Install the **macrobond** plugin. This brings both halves — the `/macrobond` skill and the
  MCP server config — in one step.
- Complete the Macrobond OAuth sign-in when prompted. The `/macrobond` skill activates
  automatically when you ask for economic data.

## Microsoft 365 Copilot

Microsoft 365 Copilot uses a different package format — a declarative agent, not a plugin. Download the ZIP and upload it as a custom app. Do not unzip it; Microsoft expects the ZIP as-is.

- [Latest release](https://github.com/macrobond-platform/macrobond-plugins/releases/latest/download/Macrobond-AI-Data-Feed-M365-Copilot-Agent.zip) *(recommended)*
- [All releases](https://github.com/macrobond-platform/macrobond-plugins/releases)
- [Full install guide](m365/README.md)

### Requirements

- A [Microsoft 365 Copilot license](https://learn.microsoft.com/microsoft-365-copilot/extensibility/prerequisites#prerequisites)
- Permission to upload custom apps in your Microsoft 365 tenant
- An active Macrobond AI Data Feed subscription

### Tenant-wide

Makes the agent available to everyone. In the [Teams admin center](https://admin.teams.microsoft.com/), go to *Teams apps → Manage apps → Actions → Upload new app*, select the ZIP and approve it. See [Upload custom apps](https://learn.microsoft.com/microsoftteams/teams-custom-app-policies-and-settings).

### Just for yourself

Open the [Microsoft 365 app](https://www.microsoft365.com/chat) or Teams, go to *Apps → Manage your apps → Upload an app → Upload a custom app*, and select the ZIP. Requires custom-app upload to be enabled for your account.

## License

See [LICENCE](LICENCE).

