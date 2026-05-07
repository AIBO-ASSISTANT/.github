# Pull Request Workflow

## Required Flow

1. Start from a scoped issue or task.
2. Create a branch from latest `main`.
3. Keep the change focused.
4. Run relevant validation locally.
5. Open a pull request using the default template.
6. Request review from the affected owner.
7. Resolve all review comments.
8. Merge only after checks and approvals pass.

## Pull Request Size

Prefer pull requests that can be reviewed in one focused pass. Split when a change mixes:

- API contract change and unrelated refactor
- UI redesign and auth behavior
- database migration and feature expansion
- workflow policy and implementation logic

## Required Evidence

Every PR must show validation. Evidence can be:

- command output summary
- test suite names
- screenshots or recordings
- API request/response examples
- migration dry-run notes
- documentation link review

## Draft PRs

Use draft PRs for early feedback. Draft PRs must not be merged.
