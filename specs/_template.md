# Spec: <Domain>

Status: draft | approved
Scope: full-stack | server-only | client-only

> For `server-only` work write "N/A" in the UI section and skip client steps.
> For `client-only` work write "N/A" in the API section and skip server steps.
> This spec is the source of truth. Change the spec first, then tests, then code.

## Goal

One paragraph on what this delivers and why.

## API

Endpoints, request/response shapes, status codes, and auth rules.

| Method | Path | Auth | Request | Response | Notes |
| ------ | ---- | ---- | ------- | -------- | ----- |
| `GET`  | `/`  | —    | —       | —        | Example row, replace me |

Error format:

```json
{ "error": { "code": "STRING", "message": "Human-readable message" } }
```

## UI

Screens, routes, components, and user interactions. Reference the API rows above.

- Route `/`: renders X, calls `GET /`, shows Y on error.

## Acceptance criteria

Number every criterion. Each needs at least one test whose name includes its ID.

- AC-1: Description of expected behavior.
- AC-2: Another expected behavior.

## Open questions

Record every assumption here. Do not build silently on an assumption.

- Q1: Unresolved question?

## Sensitive features checklist (auth, permissions, money)

Answer each, or move it to Open questions. Delete this section for non-sensitive domains.

- How are sessions or tokens issued, stored, and sent (cookie flags, expiry)?
- How are existing sessions invalidated on logout, password change, and reset?
- Which endpoints are rate limited, and how?
- Do error responses reveal whether an account exists? (They should not.)
- What is logged, and what must never be logged?
