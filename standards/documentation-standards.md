# Documentation Standards

## Quality Rules

- Use precise names and current repository terminology.
- Separate current state from planned state.
- Link to canonical docs instead of duplicating long explanations.
- Explain why decisions exist when the reason changes engineering behavior.
- Keep examples realistic and aligned with implemented APIs.
- Remove stale claims in the same PR that changes behavior.
- Avoid filler language that does not create a decision, rule, or useful explanation.

## Required Updates

Update docs when changing:

- public API
- auth/session lifecycle
- data model or database ownership
- deployment requirements
- environment variables
- security behavior
- logging/observability behavior
- engine contract or AI behavior
- repository structure

## Naming

- Markdown files use lowercase kebab-case.
- Directories use lowercase kebab-case, except GitHub-recognized template directories.
- Section names use short title case.
- Status labels must match the feature maturity matrix.

## Drift Prevention

- Do not copy large sections across docs.
- Use index files to route readers.
- Keep diagrams as Mermaid source where possible.
- Every "planned" or "future" statement should be visibly marked as such.
