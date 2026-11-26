# Macrobond plugins

Bring trusted economic and financial time-series data from Macrobond into your AI
assistant or coding environment.

This repository holds two plugins. Each connects supported clients to a hosted
Macrobond MCP service and includes a skill that teaches the assistant how to
search for series, explain selections, and retrieve data safely.

| Plugin | Install name | MCP service |
| --- | --- | --- |
| **Macrobond AI Data Feed** | `macrobond` | `https://platform.macrobond.com/mcp` |
| **Macrobond Assist** | `macrobond-assist` | `https://suite.macrobond.com/assist/mcp` |

## Choose your client

| Client | Macrobond AI Data Feed | Macrobond Assist |
| --- | --- | --- |
| VS Code | [`plugins/macrobond-ai-datafeed/agent-plugin`](plugins/macrobond-ai-datafeed/agent-plugin) | [`plugins/macrobond-assist/agent-plugin`](plugins/macrobond-assist/agent-plugin) |
| Cursor | [`plugins/macrobond-ai-datafeed/cursor`](plugins/macrobond-ai-datafeed/cursor) | [`plugins/macrobond-assist/cursor`](plugins/macrobond-assist/cursor) |
| GitHub Copilot CLI | [`plugins/macrobond-ai-datafeed/copilot`](plugins/macrobond-ai-datafeed/copilot) | [`plugins/macrobond-assist/copilot`](plugins/macrobond-assist/copilot) |
| Claude Code, Claude chat, and Cowork | [`plugins/macrobond-ai-datafeed/claude`](plugins/macrobond-ai-datafeed/claude) | [`plugins/macrobond-assist/claude`](plugins/macrobond-assist/claude) |
| ChatGPT and Codex | [`plugins/macrobond-ai-datafeed/codex`](plugins/macrobond-ai-datafeed/codex) | [`plugins/macrobond-assist/codex`](plugins/macrobond-assist/codex) |
| Microsoft 365 Copilot | [`plugins/macrobond-ai-datafeed/m365`](plugins/macrobond-ai-datafeed/m365) | [`plugins/macrobond-assist/m365`](plugins/macrobond-assist/m365) |

## What you can do

- Find economic and financial indicators using natural language.
- Retrieve time series with their metadata and source information.
- Compare indicators across countries, concepts, and time periods.
- Create clear charts and tables for research and analysis.
- Explore data revisions and historical releases.

For example:

- *"Give me a chart of Sweden GDP."*
- *"Compare US and euro area core inflation over the last ten years."*
- *"Show me a table of German unemployment, monthly, since 2020."*

## What you need

You need an active Macrobond AI Data Feed subscription or Macrobond Assist
license, a Macrobond account, and a supported client. When prompted, sign in with your Macrobond account.

## Repository contents

- [`plugins/`](plugins) contains one folder per plugin, with a package for each
  supported client and the plugin's `CHANGELOG.md`.
- `.agents/`, `.claude-plugin/`, `.cursor-plugin/`, and `.github/` contain the marketplace
  definitions used by their respective clients. Each lists both plugins.
- [`LICENCE`](LICENCE) contains the package licence.

## Releasing a new version

Push a tag on a `main` commit that names the version: `release-plugin-staging-1.2.1`
releases to `macrobond-plugins-staging` and `release-plugin-1.2.1` to `macrobond-plugins`.
Both plugins are released together, at that version. The version must be higher than
the newest release in that repository. The workflow opens a pull request there;
merging it publishes the release.

In the source repository, each plugin keeps its skill, changelog and icons at the
root of its folder. Run `pnpm sync` to copy them into the packages, and `pnpm test` to check
that the packages agree with each other and validate against their schemas.
