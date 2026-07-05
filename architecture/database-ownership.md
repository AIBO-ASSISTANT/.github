# Database Ownership

## Current Ownership

| Data | Store | Owner | Notes |
| --- | --- | --- | --- |
| Users | MongoDB | Backend | Canonical user identity and profile data. |
| User sessions | MongoDB | Backend | Refresh session metadata and revocation state. |
| Projects | MongoDB | Backend | Project ownership and project-level metadata. |
| Project members | MongoDB | Backend | Project membership and role assignments. |
| Project columns | MongoDB | Backend | Ordered project board columns. |
| Schedules | MongoDB | Backend | User-owned schedule entries. |
| Tasks | MongoDB | Backend | Task records and task ownership. |
| Task assignments | MongoDB | Backend | Multi-user task assignment rows. |
| Executions | MongoDB | Backend | Task execution history and result payloads. |
| Assets | MongoDB | Backend | Polymorphic file/object catalog. |
| Activity logs | MongoDB | Backend | Polymorphic audit and activity trail. |
| Notifications | MongoDB | Backend | User-facing notification rows. |

The canonical logical model is documented in
[database-schema.md](database-schema.md).

## Rules

- The frontend and engine never write directly to databases.
- Cross-collection references must use stable IDs and avoid hidden joins.
- Schema changes must be reviewed with rollback and data integrity in mind.
- Any move of a domain between stores requires an ADR.

## Scaling Considerations

- MongoDB schedule writes may need transaction-capable deployment for production concurrency.
- Data ownership should be optimized only after measuring real bottlenecks.
