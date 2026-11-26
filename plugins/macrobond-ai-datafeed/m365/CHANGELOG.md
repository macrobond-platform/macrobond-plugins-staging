# Changelog

## Unreleased

- Renamed the plugin Macrobond AI Data Feed, to tell it apart from Macrobond
  Assist. Its identifier stays `macrobond`, so installed plugins keep updating.
- The Microsoft 365 Copilot agent now follows the same Macrobond skill as the
  Claude plugin, in place of its own instructions.
- Moved the packages from `packages/` to `plugins/macrobond-ai-datafeed/` in the
  marketplace repository, to make room for the Macrobond Assist plugin. Installed
  plugins keep working; links to the old `packages/` paths do not.
- Removed the Gemini CLI package. Google retired Gemini CLI for personal
  accounts on 18 June 2026.
- Rewrote the skill as four ordered steps: a clarifying question only for
  generic requests, the server instructions, one search per concept and region,
  and the series picker as the only confirmation. Hosts without the picker keep
  the text confirmation as a fallback and end with a link that opens the series
  in Macrobond Analysis.
- Added Cursor support, with its own marketplace definition and sign-in
  configuration.
- Added a GitHub Copilot CLI package that carries the sign-in configuration
  Copilot CLI needs.
- Updated Microsoft 365 provisioning to keep MCP OAuth endpoints, scopes, PKCE,
  and tenant access synchronized when an existing registration is reused.

## 0.0.3

- Added Microsoft 365 Copilot support for the hosted Macrobond MCP server,
  including OAuth sign-in and server-provided usage instructions.

## 0.0.2

- Replaced the separate guidance topic calls with a single required
  `get_instructions()` call at the start of each conversation.
- Updated quota-protected fetch guidance to show the series count as the UTS
  cost and no longer report the user's UTS balance.
- Added explicit OAuth scopes for clients that require them.

## 0.0.1

- Added the Macrobond skill for searching and retrieving economic and financial
  time series.
- Added an OAuth connection to the hosted Macrobond MCP server.
- Added guidance and explicit confirmation rules for quota-protected fetches.
