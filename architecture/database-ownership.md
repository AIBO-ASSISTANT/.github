# Database Ownership

## Current Ownership

| Data | Store | Owner | Notes |
| --- | --- | --- | --- |
| Users | MongoDB | Backend | User identity source of truth. |
| Refresh sessions | MongoDB | Backend | Stores refresh session metadata and revocation state. |
| Tasks | MongoDB | Backend | User-scoped task data. |
| Schedules | MongoDB | Backend | User-scoped schedule data. |
| Projects | PostgreSQL | Backend | Relational project management module. |
| Project columns | PostgreSQL | Backend | Ordered board columns. |
| Project tasks | PostgreSQL | Backend | Ordered project tasks. |
| Project assignments | PostgreSQL | Backend | User ID references; MongoDB remains user source. |
| Project activity | PostgreSQL | Backend | Activity/audit-style records for project workflows. |

## Rules

- The frontend and engine never write directly to databases.
- PostgreSQL must not create a duplicate users table without an ADR.
- Cross-store references must use stable IDs and avoid hidden joins.
- Migrations must be reviewed with rollback and data integrity in mind.
- Any move of a domain between stores requires an ADR.

## Scaling Considerations

- MongoDB schedule writes may need transaction-capable deployment for production concurrency.
- PostgreSQL project ordering depends on integrity constraints and transactional updates.
- Data ownership should be optimized only after measuring real bottlenecks.
