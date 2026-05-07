# Branching Strategy

## Main Branch

`main` represents the latest accepted state. It must remain reviewable and buildable.

Direct pushes to `main` are prohibited unless maintainers explicitly approve emergency repair.

## Branch Names

Use:

```text
<type>/<owner>/<short-description>
```

Allowed types:

- `feature`
- `fix`
- `docs`
- `refactor`
- `test`
- `chore`
- `ci`
- `security`

Rules:

- lowercase only
- kebab-case words
- no ticket-only branch names
- no personal experiments on long-lived shared branches

Examples:

```text
feature/ashwin/project-board-ui
fix/platform/engine-timeout-handling
docs/platform/deployment-readiness
```

## Branch Lifetime

- Keep branches short-lived.
- Rebase or merge from `main` before review when conflict risk exists.
- Delete branches after merge.
- Long-running work should be split behind safe interfaces or documented milestones.
