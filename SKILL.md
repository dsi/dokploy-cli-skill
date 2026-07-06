---
name: dokploy-cli
description: Knowledge of the Dokploy CLI (@dokploy/cli, github.com/Dokploy/cli) for remotely managing a Dokploy server — deploying apps/compose stacks, managing databases (postgres/mysql/mariadb/mongo/redis), domains, backups, and server settings from the terminal. Use this whenever the user asks about installing, authenticating, or running the `dokploy` command; wants to know what command groups/actions exist; or wants to write, debug, or automate shell scripts that call `dokploy` (e.g. CI/CD deploy scripts, cron backup jobs, bulk operations across projects). Trigger even if they just say "dokploy" or paste a `dokploy ...` command with an error. Do NOT use for the Dokploy web panel, its HTTP API directly, or the separate Dokploy MCP server — this skill is specifically the CLI tool.
---

# Dokploy CLI

Source: https://github.com/Dokploy/cli (MIT). Two reference files.

- `references/commands.md` — group list + command count per group, dev/build workflow. Load for "how many commands does X have" or contributing questions.
- `references/full-reference.md` — every one of the 449 commands with its exact flags (required vs optional, boolean vs string), grouped by command group. Load this whenever you need a real action name or flag to write/debug a command — don't load either file for simple auth/usage questions, the core info is below.

Individual flag _value formats_ (e.g. exact enum strings, comma- vs repeat-flag for lists) are still not verified — confirm those with `dokploy <group> <action> --help` or `repo/cli/openapi.json` before relying on them.

## What it is

A Node.js CLI that manages a **remote, already-running** Dokploy server over its HTTP API — it does not install Dokploy locally. 449 commands across 43 groups, auto-generated from Dokploy's OpenAPI spec, structured as `dokploy <group> <action> [--flags]`.

## Installation

```bash
npm install -g @dokploy/cli
```

## Authentication

Three methods. Precedence (highest first): **shell env vars → `.env` file in cwd → stored `config.json` written by `auth`.** So a set env var overrides a prior `auth`, not just supplements it. The token env var is `DOKPLOY_API_KEY`, but `DOKPLOY_AUTH_TOKEN` is also accepted as a fallback name — and it's the name the CLI's own error messages tell users to set, so expect to see it in pasted errors.

```bash
dokploy auth -u https://panel.dokploy.com -t YOUR_API_KEY   # writes stored credentials
```

```bash
export DOKPLOY_URL="https://panel.dokploy.com"
export DOKPLOY_API_KEY="YOUR_API_KEY"
```

```
# .env in cwd, loaded automatically
DOKPLOY_URL="https://panel.dokploy.com"
DOKPLOY_API_KEY="YOUR_API_KEY"
```

For scripts/CI, prefer env vars (`DOKPLOY_URL`, `DOKPLOY_API_KEY`) injected as secrets — don't write `auth` with a hardcoded key into a script.

## Usage pattern

```bash
dokploy <group> <action> [options]
```

```bash
dokploy project all
dokploy project one --projectId abc123
dokploy application create --name "my-app" --environmentId env123
dokploy application deploy --applicationId app123
dokploy postgres create --name "my-db" --databaseName mydb --databaseUser admin --databasePassword secret --environmentId env123
dokploy postgres stop --postgresId pg123
dokploy project all --json
```

### Discovering commands/flags

Look up the action and its flags in `references/full-reference.md` first — it has all 449 commands verified from source. Only fall back to running `--help` yourself when you need something the reference doesn't cover (exact value format for a flag, confirming behavior, or the repo isn't checked out in the current environment):

```bash
dokploy --help
dokploy application --help
dokploy application deploy --help
```

Never invent a flag name or action that isn't in `full-reference.md` or confirmed via `--help`.

## Writing/debugging scripts against the CLI

- **Use `--json` for anything scripted.** Human-readable output is not meant to be parsed; `--json` gives stable machine-readable output for `jq`/`grep`/further processing.
  ```bash
  app_id=$(dokploy application search --json | jq -r '.[] | select(.name=="my-app") | .applicationId')
  ```
- **Check exit codes**, don't assume success. Wrap calls:
  ```bash
  if ! dokploy application deploy --applicationId "$app_id"; then
    echo "deploy failed for $app_id" >&2
    exit 1
  fi
  ```
- **Bulk/loop operations** (e.g. "redeploy every app in a project"): fetch the list with `--json`, loop with a shell/jq pipeline, call the per-item action inside the loop. Confirm the exact ID field name (`applicationId`, `projectId`, etc.) from actual `--json` output rather than assuming — field names may not match the flag names 1:1.
- **CI/CD**: install globally in the pipeline (`npm install -g @dokploy/cli`), authenticate via env var secrets, then call `deploy`/`compose deploy` actions. No native GitHub Action is documented in the README — it's invoked as a plain CLI step.
- **Cron/scheduled jobs** (e.g. nightly backups): `backup` group (11 actions, see `full-reference.md`) plus the relevant database group's `deploy`/`change-status` actions — `create` on the `backup` group takes `--schedule` (cron string), `--destinationId`, `--database`, `--databaseType`, and one of `--postgresId`/`--mysqlId`/`--mariadbId`/`--mongoId`/`--userId` depending on `--databaseType`.
- **Debugging a failing command a user pastes**: check for (1) missing/expired auth — `DOKPLOY_URL`/`DOKPLOY_API_KEY` set and valid, (2) wrong group/action name — cross-check against `full-reference.md` (or `--help` if the repo isn't available), (3) required flag missing — check the `req:` list in `full-reference.md` for that command; a missing required flag usually errors clearly rather than silently.

## Related, do not confuse

- **Dokploy panel/server** (github.com/Dokploy/dokploy): the actual PaaS this CLI controls.
- **Dokploy MCP server** (github.com/Dokploy/mcp): ~508 tools for MCP clients — same underlying API, different interface, separate project.

## Support

Issues: https://github.com/Dokploy/cli/issues · Contributing: CONTRIBUTING.md in repo · License: MIT
