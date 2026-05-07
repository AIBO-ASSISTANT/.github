# Contributing to AIBO

This repository defines the shared contribution model for the AIBO ecosystem. Service repositories may add local instructions, but they must not contradict these rules.

## Contribution Principles

- Make the smallest responsible change that solves the stated problem.
- Preserve existing architecture unless the pull request explicitly proposes a better one.
- Document behavior that affects service contracts, security, deployment, operations, or onboarding.
- Do not claim future features as implemented.
- Keep implementation and documentation in the same pull request when behavior changes.

## Branching

Create branches from the latest `main`. Direct pushes to `main` are not allowed.

Use lowercase kebab-case:

```text
feature/<owner>/<short-description>
fix/<owner>/<short-description>
chore/<owner>/<short-description>
docs/<owner>/<short-description>
refactor/<owner>/<short-description>
security/<owner>/<short-description>
```

Examples:

```text
feature/ashwin/auth-dashboard-route
fix/ashwin/refresh-cookie-validation
docs/platform/api-error-standards
```

See [standards/branching-strategy.md](standards/branching-strategy.md).

## Commits

Use Conventional Commits:

```text
<type>(optional-scope): <summary>
```

Allowed common types:

- `feat`
- `fix`
- `docs`
- `refactor`
- `test`
- `chore`
- `ci`
- `security`

Examples:

```text
feat(auth): restore browser session from refresh cookie
fix(engine): return clarification for ambiguous scheduling text
docs(api): define validation error response format
```

Keep commits focused. Avoid vague summaries such as `update`, `fixes`, or `changes`.

## Pull Requests

Every code, documentation, workflow, or policy change must go through a pull request.

Pull requests must include:

- clear problem statement
- summary of changes
- testing or validation proof
- screenshots or recordings for UI changes
- security impact
- architecture impact
- breaking-change assessment
- documentation updates or a reason none are needed

Use [PULL_REQUEST_TEMPLATE.md](PULL_REQUEST_TEMPLATE.md).

## Review Rules

- At least one approval is required before merge.
- Risky changes require review from the owning area.
- Authors must not merge their own pull request without approval.
- Review comments must be resolved by code, docs, or an explicit written decision.
- Large pull requests should be split unless the coupling is unavoidable and explained.

See [standards/code-review-standards.md](standards/code-review-standards.md).

## Required Validation

Run the checks that apply to the repository changed:

| Repository | Minimum local validation |
| --- | --- |
| `AIBO-BACKEND` | `npm run lint`, `npm run test` |
| `AIBO-FRONTEND` | `npm run lint`, `npm test`, `npm run build` when UI/build code changes |
| `AIBO-ENGINE` | `python -m unittest discover -s tests` |
| `.github` | review links, naming, issue forms, workflow syntax, and documentation status claims |

If a check cannot be run, explain why in the pull request and identify the residual risk.

## Documentation Discipline

Update documentation when a change affects:

- API shape or response semantics
- auth/session behavior
- repository boundaries
- environment variables
- deployment or operational behavior
- security posture
- supported feature maturity
- onboarding steps

The feature maturity matrix must stay accurate. If a feature moves from planned to partial or implemented, update [docs/product/feature-maturity-matrix.md](docs/product/feature-maturity-matrix.md).

## Architecture Decisions

Create an ADR when a change:

- changes service boundaries
- introduces a new runtime dependency
- changes database ownership
- changes auth/session strategy
- introduces deployment infrastructure
- affects production safety or scaling strategy

Use [adr/0000-template.md](adr/0000-template.md).
