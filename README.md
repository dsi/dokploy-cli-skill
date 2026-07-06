# dokploy-cli skill

A skill that provides working knowledge of the **Dokploy CLI** ([`@dokploy/cli`](https://github.com/Dokploy/cli)) — the Node.js command-line tool for remotely managing a Dokploy server.

It covers installing and authenticating the CLI, looking up any of its **449 commands across 43 groups** with exact flags, and writing or debugging shell scripts that drive `dokploy` (CI/CD deploys, cron backups, bulk operations).

> This skill is about the **CLI tool** only — not the Dokploy web panel, its HTTP API directly, or the separate [Dokploy MCP server](https://github.com/Dokploy/mcp).

## What's inside

| File | Purpose |
| --- | --- |
| `SKILL.md` | Skill entry point — install, auth, usage patterns, and scripting guidance. |
| `references/commands.md` | Command-group list with per-group command counts and the dev/build workflow. |
| `references/full-reference.md` | Every one of the 449 commands with its exact required/optional flags, grouped by command group. |

## Scope

The skill is relevant whenever you need to:

- install, authenticate, or run the `dokploy` command
- find out what command groups or actions exist
- write, debug, or automate scripts that call `dokploy`
- diagnose a `dokploy ...` command or error

## The CLI itself

If you just want the underlying tool:

```bash
npm install -g @dokploy/cli
dokploy auth -u https://panel.dokploy.com -t YOUR_API_KEY
dokploy project all
```

See `SKILL.md` for authentication precedence (env vars → `.env` → stored config), the `dokploy <group> <action> [--flags]` pattern, and scripting tips like using `--json` for machine-readable output.

## Sources

- Dokploy CLI: <https://github.com/Dokploy/cli> (MIT)
- Dokploy panel/server: <https://github.com/Dokploy/dokploy>

## License

MIT — see [LICENSE](LICENSE).
