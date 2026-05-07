# Changelog Automation Strategy

## Current State

Changelog is manual.

## Target Strategy

1. Enforce Conventional Commits.
2. Label pull requests by area and impact.
3. Generate draft release notes from merged PRs.
4. Keep manual review before publishing.
5. Link release notes to affected repositories and migrations.

## Required Inputs

- commit type
- PR labels
- breaking change marker
- migration notes
- affected repository
- security impact

## Rule

Automation may draft changelogs, but maintainers remain responsible for accuracy.
