# Release Governance

## Semantic Versioning Strategy

Use semantic versioning once service repositories begin formal releases.

- `MAJOR`: breaking API, auth, data, or deployment behavior
- `MINOR`: backward-compatible features
- `PATCH`: backward-compatible fixes

## Release Workflow Foundation

Release workflows should:

- validate changelog entry
- identify affected repository
- run required checks
- create release notes
- require approval for production deployment
- avoid deploying from unreviewed branches

## Deployment Workflows

Deployment workflows are placeholders until:

- environments are defined
- secrets are configured
- health checks exist
- rollback steps are tested
- owners are assigned

## Release Notes

Use [../templates/release-notes-template.md](../templates/release-notes-template.md).
