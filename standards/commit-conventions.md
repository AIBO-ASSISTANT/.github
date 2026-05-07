# Commit Conventions

AIBO uses Conventional Commits so changelog automation and release notes can become reliable later.

## Format

```text
<type>(<scope>): <summary>
```

Scope is optional but recommended.

## Types

| Type | Use |
| --- | --- |
| `feat` | User-visible capability or API feature. |
| `fix` | Bug fix. |
| `docs` | Documentation-only change. |
| `refactor` | Behavior-preserving structure change. |
| `test` | Test addition or correction. |
| `chore` | Maintenance with no runtime behavior change. |
| `ci` | Workflow, build, or automation change. |
| `security` | Security hardening or vulnerability fix. |

## Breaking Changes

Use `!` and explain migration in the commit body:

```text
feat(api)!: require project role on column reorder
```

## Bad Examples

- `update`
- `fix stuff`
- `changes`
- `final`
- `working`

These do not support review, rollback, or release notes.
