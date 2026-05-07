# Dependency Management

## Dependency Rules

- Add dependencies only when they remove meaningful complexity or provide a proven domain capability.
- Prefer existing repository libraries and patterns.
- Review license, maintenance status, security posture, and runtime cost.
- Do not add infrastructure dependencies without an ADR.
- Pin through lockfiles where package managers support it.

## Backend

- Use `npm ci` in CI.
- Review Express middleware for ordering and security impact.
- Treat auth, validation, logging, and database dependencies as security-sensitive.

## Frontend

- Avoid adding global state or UI libraries until feature complexity requires them.
- Keep bundle impact in mind.
- Prefer accessible, composable components.

## Engine

- Add a dependency manifest before broadening Python dependencies.
- Model/provider SDKs require an ADR, evaluation strategy, and fallback plan.

## Dependency Updates

- Security updates should be prioritized.
- Major upgrades require release notes and compatibility validation.
- Lockfile-only changes still require review.
