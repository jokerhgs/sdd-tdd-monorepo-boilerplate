# AGENTS.md

Monorepo with two packages: `server/`and `client/`.

This file defines the **workflow** for AI agents and contributors. Code rules live in [`server/AGENTS.md`](./server/AGENTS.md) and [`client/AGENTS.md`](./client/AGENTS.md). When working in a package, follow both this file and that package's file. This file wins on process; the package file wins on code style.

## Layout

```
requirements/<domain>.md   # What we want (human-written)
requirements/_template.md    # Requirements format
specs/<domain>.md          # Exact behavior: API + UI (agent drafts, human approves)
specs/_template.md         # Spec format
specs/README.md            # Domain index (scope + status per domain)
server/                    # Backend (own AGENTS.md)
client/                    # Frontend (own AGENTS.md)
```

`<domain>` is the same name everywhere: `requirements/auth.md`, `specs/auth.md`, `server/src/modules/auth/`, `client/src/features/auth/`.

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

## Workflow (spec-driven + TDD)

Order is always: **requirements → spec (human approves) → server tests → server code → refactor → client tests → client code → refactor → verify.**
The approved spec is the source of truth. Tests and code are derived from it.

1. **Find the domain.** Match the request to a domain in `requirements/`. If none fits, or the feature spans more than one domain, ask the human first. Never pick silently.
2. **Update requirements.** Add the feature to `requirements/<domain>.md`. Do not add anything listed under "Out of scope".
3. **Draft the spec.** Edit the existing `specs/<domain>.md` using `specs/_template.md`. Never create a second spec for the same domain. Write the API section, the UI section, and numbered acceptance criteria. Record every assumption under "Open questions".
4. **STOP.** Show the spec diff and wait for approval. Do not write tests or code until the human approves.
5. **Server first (red → green → refactor).** Write failing acceptance tests from the spec's API section and acceptance criteria, plus failing unit tests for pure logic and branching (hashing, normalization, validators, counters). Implement until all pass, then refactor with no behavior change while the suite stays green.
6. **Then client (red → green → refactor).** Write failing acceptance tests from the spec's UI section and acceptance criteria, plus failing unit tests for client-side logic and branching. Implement against the real API types until all pass, then refactor with no behavior change while the suite stays green.
7. **Verify** from the root: `pnpm verify` (`pnpm typecheck && pnpm lint && pnpm test`).
8. **Report.** Summarize files changed, assumptions made, and any dependencies added.

### Feature scope

Each spec declares `Scope: full-stack | server-only | client-only`. For server-only or client-only work, write "N/A" in the other section and skip that side's steps.

### Acceptance criteria

Number them (`AC-1`, `AC-2`, ...). Every criterion needs at least one test, and the test name includes its ID. A criterion without a test, or a test without a criterion, is a gap to raise.

### TDD cycle (outer + inner)

- Outer loop (mandatory, spec-bound): one or more failing acceptance tests per `AC-N`, named with its ID, red before implementation.
- Inner loop (mandatory for logic): failing unit tests for pure functions and branching before implementation. Test-runner and mocking specifics live in `server/AGENTS.md` and `client/AGENTS.md` so cloned stacks stay flexible.

### Refactor

- After each green, refactor with no behavior change while the full suite stays green. No spec change is needed for behavior-preserving refactors (see Rules). If a refactor needs a behavior change, change the spec first, then the tests, then the code.

## Rules

- If behavior needs to change, change the spec first, then the tests, then the code.
- Never edit an existing test just to make it pass. If a test looks wrong, tell the human and propose a spec change.
- Do not add features, endpoints, screens, or dependencies that are not in the approved spec.
- Low-risk change (typo, refactor with no behavior change, dependency bump): no spec needed. Say so and proceed.
- Existing code without a spec: when asked, read the code, draft `specs/<domain>.md` describing what it does today, and flag suspected bugs or undocumented behavior. Do not change behavior while backfilling.
- Never commit secrets. `.env` files and `.dev.vars` stay out of git.

## Boundaries

- Server tasks stay in `server/`. Client tasks stay in `client/`. Root files (requirements, specs, workspace config) change only as part of the workflow or when asked.
- If the client needs a different API, update the spec's API section, get approval, then change the server first.
- The API contract is the spec's API section. The client consumes it through the server's exported `AppType` and Hono's typed client. The import mechanism is set once in `client/src/lib/api.ts`. Do not change tsconfig, package exports, or route structure to work around a typing problem without approval.
- Do not hand-write client types for API shapes the server already defines.

## Requirements format

Keep requirements short:

```markdown
# <Domain> requirements

Goal: one sentence on why this exists.

- Plain-language requirement
- Another requirement

Out of scope:

- Things the agent must not build
```

## Specs for sensitive features (auth, permissions, money)

Review these more strictly. The draft spec must answer, or list as open questions, each of these:

- How are sessions or tokens issued, stored, and sent (cookie flags, expiry)?
- How are existing sessions invalidated on logout, password change, and reset?
- Which endpoints are rate limited, and how?
- Do error responses reveal whether an account exists? (They should not.)
- What is logged, and what must never be logged?

Implementation rules for these live in `server/AGENTS.md` and `client/AGENTS.md`.

## Definition of done

- The spec status is `approved` and matches the shipped behavior.
- Every acceptance criterion has a passing test.
- `pnpm typecheck`, `pnpm lint`, and `pnpm test` pass from the root.
- No existing test was weakened or removed to get a green run.
- The final report lists files changed, assumptions, and dependencies added.
