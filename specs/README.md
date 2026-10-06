# Specs domain index

One spec per domain (bounded context). The `<domain>` name is the same everywhere:
`requirements/<domain>.md`, `specs/<domain>.md`, `server/src/modules/<domain>/`, `client/src/features/<domain>/`.

Naming: lowercase singular (e.g. `auth`, `todo`).

| Domain | Scope | Status | Spec |
| ------ | ----- | ------ | ---- |
| _auth_ | full-stack \| server-only \| client-only | draft \| approved | `specs/auth.md` |
| _todo_ | full-stack \| server-only \| client-only | draft \| approved | `specs/todo.md` |

Rules:

- Never create a second spec for the same domain. Extend the existing `specs/<domain>.md`.
- `Status: draft` means under review — no tests or code until the human approves.
- `Status: approved` is the source of truth tests and code are derived from.
- Cross-domain features live in the consuming domain's spec and reference the other — never duplicate.
- Format for new specs: `specs/_template.md`.
