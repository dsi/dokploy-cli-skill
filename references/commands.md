# Dokploy CLI — command group reference

449 commands across 43 groups, auto-generated from Dokploy's OpenAPI spec (`openapi.json` in the repo). Table below is group name + command count, for a quick "how big is this group" lookup. For the actual action names and flags per command, see `references/full-reference.md` — it's parsed directly from `src/generated/commands.ts` and covers all 449 commands.

| Group        | #   | Group              | #   |
| ------------ | --- | ------------------ | --- |
| admin        | 1   | notification       | 38  |
| ai           | 9   | organization       | 10  |
| application  | 29  | patch              | 12  |
| backup       | 11  | port               | 4   |
| bitbucket    | 7   | postgres           | 14  |
| certificates | 4   | preview-deployment | 4   |
| cluster      | 4   | project            | 8   |
| compose      | 28  | redirects          | 4   |
| deployment   | 8   | redis              | 14  |
| destination  | 6   | registry           | 7   |
| docker       | 7   | rollback           | 2   |
| domain       | 9   | schedule           | 6   |
| environment  | 7   | security           | 4   |
| gitea        | 8   | server             | 16  |
| github       | 6   | settings           | 49  |
| gitlab       | 7   | ssh-key            | 6   |
| git-provider | 2   | sso                | 10  |
| license-key  | 6   | stripe             | 7   |
| mariadb      | 14  | swarm              | 3   |
| mongo        | 14  | user               | 18  |
| mounts       | 6   | volume-backups     | 6   |
| mysql        | 14  |                    |     |

Observations (verified from source — see `full-reference.md`):

- `postgres`, `mysql`, `mariadb`, `mongo`, `redis` each have exactly the same 14 actions: `change-status`, `create`, `deploy`, `move`, `one`, `rebuild`, `reload`, `remove`, `save-environment`, `save-external-port`, `search`, `start`, `stop`, `update`. Fully symmetric across all five database types — swap the group name and `--<type>Id` flag to move between them.
- `application` (29) and `compose` (28) are the largest workload groups — core deploy surfaces.
- `settings` (49) and `notification` (38) are the two largest groups overall.
- Each git provider (`bitbucket`, `gitea`, `github`, `gitlab`) is a separate group from generic `git-provider`, which just has `get-all` (no args) and `remove --gitProviderId`.

## Dev/build commands (for contributing to the CLI itself)

```bash
pnpm install
pnpm run dev -- project all   # run in dev mode
pnpm run generate              # regenerate commands from openapi.json
pnpm run build
pnpm run lint
```

Update flow when Dokploy's API changes: replace `openapi.json` with the latest spec from Dokploy/dokploy → `pnpm run generate` → `pnpm run build`. Re-run this after regenerating: it's what produced `full-reference.md`.

Known exception: `auth` (`src/commands/auth.ts`) is hand-authored, not generated from the OpenAPI spec — all 449 other commands (`src/generated/commands.ts`) follow one mechanical pattern (see `full-reference.md`).
