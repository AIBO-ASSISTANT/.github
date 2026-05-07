# Versioning and Deprecation

## Current Version

The backend API currently uses `/api/v1`.

## Versioning Rules

- Additive fields are usually backward-compatible.
- Removing fields is breaking.
- Changing field meaning is breaking.
- Tightening validation can be breaking if existing valid clients fail.
- Auth/session behavior changes are breaking unless clients remain compatible.

## Deprecation Process

1. Document the replacement.
2. Add warnings in docs and release notes.
3. Keep the old behavior for a defined window when practical.
4. Add tests that prove both old and new behavior during the window.
5. Remove only after the deprecation period is complete and communicated.

## Required Deprecation Notes

- affected endpoint
- old behavior
- new behavior
- client migration steps
- planned removal date or release
- compatibility risks
