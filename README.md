# Techpool for Claude

Techpool's Claude plugin. It installs two things:

- the **Techpool connector** (`https://mcp.techpool.tech/mcp`), and
- the **techpool skill**, which teaches Claude the main field service jobs: missions and schedules, field work reports, timesheets, expenses, invoices and clients.

Your company needs the Techpool AI Apps add-on (Settings > AI Apps in Techpool). Each person signs in to Techpool once, the first time Claude uses the connector.

## Install for your whole company (Claude Team or Enterprise)

Your admin installs it once and every tech has it.

1. Download [techpool-claude-plugin.zip](https://www.techpool.tech/downloads/techpool-claude-plugin.zip).
2. In Claude, open **Organization settings > Plugins & skills > Add > Upload a plugin** and choose the zip.
3. Set **Default access** to **Installed by default**.

## Install for yourself (Claude Code)

```bash
claude plugin marketplace add techpool-inc/techpool-claude-plugin
```

```bash
claude plugin install techpool@techpool
```

## How it stays current

`plugins/techpool/` is generated, never hand-edited. `scripts/generate.mjs` builds it from the Techpool MCP server's tool catalog and its companion skill. CI (`check-plugin`) fails the moment the server's tools and this plugin disagree, and it also runs daily.

```bash
node scripts/generate.mjs --source ../techpool-server/mcp --zip dist/techpool.zip
```

## Links

- Documentation: https://www.techpool.tech/ai-apps
- Privacy: https://www.techpool.tech/privacy
- Terms: https://www.techpool.tech/terms
- Support: https://www.techpool.tech/support
