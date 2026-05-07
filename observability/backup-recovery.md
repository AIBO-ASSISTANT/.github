# Backup and Recovery Philosophy

No production backup automation is implemented today.

## Requirements Before Production

- Define backup scope for MongoDB and PostgreSQL.
- Define retention period.
- Define restore process.
- Test restore in non-production.
- Document who can access backups.
- Ensure backups are encrypted where platform supports it.

## Recovery Objectives

RPO and RTO are not defined yet. They must be established before production launch.

## Data Stores

- MongoDB backup strategy must cover users, sessions, tasks, and schedules.
- PostgreSQL backup strategy must cover project management data and migrations.
