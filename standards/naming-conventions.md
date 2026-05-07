# Naming Conventions

## Repositories

Use canonical repository names:

- `.github`
- `AIBO-BACKEND`
- `AIBO-FRONTEND`
- `AIBO-ENGINE`

## Documentation

- Directories: `lowercase-kebab-case`
- Markdown files: `lowercase-kebab-case.md`
- GitHub template directories: `ISSUE_TEMPLATE`, `PULL_REQUEST_TEMPLATE`
- ADR files: `NNNN-short-title.md`
- Mermaid diagrams: `short-title.mmd`

## API Terminology

- Use `access token` for short-lived bearer token.
- Use `refresh token` for HttpOnly cookie-backed browser session token.
- Use `requestId` for response and log correlation.
- Use `engine` for deterministic AI domain service.
- Use `classification` for engine intent output.
- Use `action candidate` for engine-routed proposed work.

## Status Terminology

Use only:

- `Implemented`
- `Partial`
- `Planned`
- `Prototype`
- `Experimental`

Do not invent similar labels such as "done-ish", "almost ready", or "enterprise-ready".
