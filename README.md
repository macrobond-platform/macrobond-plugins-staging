# macrobond-plugin

Plugin that provides the `/macrobond` skill for Claude Code and GitHub Copilot CLI. Used together with `macrobond-mcp` — the skill provides the instructions and context for the AI agent, while the MCP server (deployed remotely) provides the tools it calls. The plugin ships a `.mcp.json` that configures the client to connect to the remote server; the server itself is not bundled.

## Table of contents

- [Install from marketplace](#install-from-marketplace)
  - [Requirements](#requirements)
  - [Claude Code](#claude-code)
  - [GitHub Copilot CLI](#github-copilot-cli)
- [Install from ZIP](#install-from-zip)
  - [Requirements](#requirements-1)
  - [Claude Code](#claude-code-1)
  - [GitHub Copilot CLI](#github-copilot-cli-1)
  - [Credentials](#credentials)
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

### Credentials

The MCP server requires Macrobond API credentials set as environment variables:

Set them persistently for your user (no admin rights needed). Restart your terminal afterwards so the new values are picked up.

**Windows (PowerShell)**

```powershell
[Environment]::SetEnvironmentVariable('MACROBOND_CLIENT_ID', '<your-client-id>', 'User')
[Environment]::SetEnvironmentVariable('MACROBOND_CLIENT_SECRET', '<your-client-secret>', 'User')
```

**macOS (zsh)**

```bash
echo 'export MACROBOND_CLIENT_ID="<your-client-id>"' >> ~/.zshrc
echo 'export MACROBOND_CLIENT_SECRET="<your-client-secret>"' >> ~/.zshrc
```

If you use bash instead, replace `~/.zshrc` with `~/.bash_profile`.

### License

See [LICENCE](./LICENCE).

