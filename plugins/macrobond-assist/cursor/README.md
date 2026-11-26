# Macrobond Assist for Cursor

Search and retrieve economic time series data with Macrobond Assist in Cursor.

This package includes the Macrobond Assist skill and configures the hosted Macrobond
Assist MCP service.

## Requirements

- An active Macrobond AI Data Feed subscription or Macrobond Assist license, and a Macrobond account
- Cursor 2.5 or later

## Install

Add the Macrobond marketplace and install the plugin:

1. Open **Customize**.
2. Select **From GitHub repository** and enter
   `https://github.com/macrobond-platform/macrobond-plugins-staging/tree/main/plugins/macrobond-assist/cursor/`.
3. Go to **Browse marketplaces**, find **Macrobond Assist**, and add it.

For a local checkout, copy this directory to
`~/.cursor/plugins/local/macrobond-assist`, then reload Cursor.

## Connect your Macrobond account

Authenticate the `macrobond-assist-staging` server and sign in with your Macrobond account.

## Try it

- *"Give me a chart of Sweden GDP."*
- *"Compare US and euro area core inflation over the last ten years."*
- *"Show me a table of German unemployment, monthly, since 2020."*

## Changelog

See [`CHANGELOG.md`](CHANGELOG.md).

## License

See [`LICENCE`](LICENCE).
