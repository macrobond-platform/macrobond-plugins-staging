# macrobond-plugin

Plugin that provides the `/macrobond` skill for Claude Code and GitHub Copilot CLI. Used together with `macrobond-mcp` — the skill provides the instructions and context for the AI agent, while the MCP server (deployed remotely) provides the tools it calls. The plugin ships a `.mcp.json` that configures the client to connect to the remote server; the server itself is not bundled.

## Install from marketplace

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

Use this if you received the plugin as a ZIP archive (e.g. without GitHub access). Download the ZIP and extract it to a folder of your choice.

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

