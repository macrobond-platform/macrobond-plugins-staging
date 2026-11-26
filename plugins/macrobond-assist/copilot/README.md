# Macrobond Assist for GitHub Copilot CLI

Search and retrieve economic time series data with Macrobond Assist in GitHub Copilot
CLI.

This package includes the Macrobond Assist skill and configures the hosted Macrobond
Assist MCP service. It carries the sign-in configuration Copilot CLI needs, which
the Agent Plugins 1.0 format has no field for.

## Requirements

- An active Macrobond AI Data Feed subscription or Macrobond Assist license, and a Macrobond account
- GitHub Copilot CLI

## Install

Add the Macrobond marketplace and install the plugin:

```text
copilot plugin marketplace add macrobond-platform/macrobond-plugins-staging
copilot plugin install macrobond-assist@macrobond-plugins-staging
```

## Connect your Macrobond account

Ask Copilot to use Macrobond Assist. If it asks you to sign in, follow the prompt and
use your Macrobond account.

## Try it

- *"Give me a chart of Sweden GDP."*
- *"Compare US and euro area core inflation over the last ten years."*
- *"Show me a table of German unemployment, monthly, since 2020."*

## Changelog

See [`CHANGELOG.md`](CHANGELOG.md).

## License

See [`LICENCE`](LICENCE).
