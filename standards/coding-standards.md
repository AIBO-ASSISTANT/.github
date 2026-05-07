# Coding Standards

## Shared Rules

- Follow the existing repository structure before adding new patterns.
- Keep controllers, route handlers, components, and services focused.
- Validate external input at boundaries.
- Return consistent error shapes from APIs.
- Keep secrets and credentials out of source code, test fixtures, logs, and docs.
- Prefer named functions for non-trivial logic.
- Remove dead code instead of commenting it out.
- Add comments only when they explain non-obvious intent or tradeoffs.

## Backend

- Routes define HTTP shape and middleware composition.
- Controllers translate request/response concerns.
- Services own business logic.
- Models own persistence structure.
- Validation schemas must reject unexpected unsafe fields.
- Database writes must be scoped to authenticated users or authorized projects.

## Frontend

- Components should not call Axios directly; use shared services.
- Feature-specific code belongs under `src/features/<feature>`.
- Shared components must avoid hidden domain assumptions.
- Access tokens remain memory-only.
- UI state must not become the source of truth for persisted data.

## Engine

- Domain logic belongs under `src/`.
- Transport adapters must remain thin.
- Classification and pipeline schemas must stay canonical.
- Ambiguous input should produce clarification behavior rather than false certainty.
