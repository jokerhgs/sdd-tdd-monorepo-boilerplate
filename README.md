# Monorepo SDD/TDD Boilerplate

Spec-driven + TDD monorepo template. Workflow is defined in [`AGENTS.md`](./AGENTS.md) — that file wins on process.

## Layout

```
requirements/<domain>.md   # What we want (human-written)
specs/<domain>.md          # Exact behavior: API + UI (agent drafts, human approves)
specs/_template.md         # Spec format
server/                    # Backend (own AGENTS.md once cloned)
client/                    # Frontend (own AGENTS.md once cloned)
```

`<domain>` is the same name everywhere: `requirements/auth.md`, `specs/auth.md`, `server/src/modules/auth/`, `client/src/features/auth/`.

## Using this template

1. Clone this repo, then clone your chosen stacks into `client/` and `server/` (and any other workspace folders you add to `pnpm-workspace.yaml`).
2. Each cloned package MUST expose these scripts (they can be no-ops if not applicable):
   `dev`, `typecheck`, `lint`, `test`, `format`, `format:check`.
3. Root scripts only delegate — root owns no ESLint/Prettier/TS config so clones stay flexible.

## Commands (run from the root)

```bash
pnpm install        # install all workspaces
pnpm dev            # run server and client together
pnpm typecheck      # typecheck both packages
pnpm lint           # lint both packages
pnpm test           # run both test suites
pnpm verify         # typecheck + lint + test
pnpm format         # format both packages
pnpm format:check   # check formatting in both packages
```

Run one package: `pnpm --filter server <script>` or `pnpm --filter client <script>`.

## Requirements

- Node.js (see `.node-version`)
- pnpm 10 (`corepack enable` if needed)

## Notes

- `requirements/` and `specs/` are intentionally NOT pnpm workspaces.
- `.env` files and `.dev.vars` stay out of git (see `.gitignore`).
