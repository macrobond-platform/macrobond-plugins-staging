# Macrobond AI Data Feed for GitHub Copilot CLI

Search and retrieve economic time series data from Macrobond in GitHub Copilot
CLI.

This package includes the Macrobond AI Data Feed skill and configures the hosted
Macrobond AI Data Feed MCP service. It carries the sign-in configuration Copilot CLI
needs, which the Agent Plugins 1.0 format has no field for.

## Requirements

- An active Macrobond AI Data Feed subscription or Macrobond Assist license, and a Macrobond account
- GitHub Copilot CLI

## Install

Add the Macrobond marketplace and install the plugin:

```text
copilot plugin marketplace add macrobond-platform/macrobond-plugins-staging
copilot plugin install macrobond@macrobond-plugins-staging
```

## Connect your Macrobond account

Ask Copilot to use Macrobond AI Data Feed. If it asks you to sign in, follow the prompt and
use your Macrobond account.

## Try it

- *"Give me a chart of Sweden GDP."*
- *"Compare US and euro area core inflation over the last ten years."*
- *"Show me a table of German unemployment, monthly, since 2020."*

## Changelog

See [`CHANGELOG.md`](CHANGELOG.md).

## License

See [`LICENCE`](LICENCE).
