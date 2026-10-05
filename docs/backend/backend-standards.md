# Backend Standards

`AIBO-BACKEND` is the authoritative API perimeter, owning authentication, session lifecycle, request validation, business domain persistence, WebSocket broadcasts, and cognitive engine orchestration.

---

## Architecture & Technology Stack

- **Runtime**: Node.js 22+ LTS
- **Framework**: Express 5 with async route handler support
- **Persistence**: MongoDB 7.0 Community (`rs0` replica set) via Mongoose
- **Caching & Ephemeral State**: Redis 7 via `ioredis`
- **Validation**: Strict schema validation with `Zod`
- **Logging**: Asynchronous JSON structured logging with `pino`
- **Testing**: Jest unit and integration tests (343 tests) + Cross-repo E2E scenarios (92 scenarios)

---

## API & Response Conventions

- All public API routes live under `/api/v1/*`.
- Standard success envelope:
  ```json
  {
    "success": true,
    "message": "Operation completed",
    "data": {},
    "requestId": "req_01h9x4b9zk3g8n1m2a3c4d5e6f"
  }
  ```
- Standard error envelope:
  ```json
  {
    "success": false,
    "message": "Validation failed",
    "data": null,
    "requestId": "req_01h9x4b9zk3g8n1m2a3c4d5e6f",
    "error": {
      "code": "VALIDATION_ERROR",
      "message": "Validation failed",
      "details": [
        { "field": "title", "issue": "Required string missing" }
      ]
    }
  }
  ```

---

## Validation & Boundary Defenses

1. **Zod Schema Gate**: Every incoming route validates `req.params`, `req.query`, and `req.body` against compiled Zod schemas before touching any service or database layer.
2. **Payload Sanitization**: Reject unknown or extra fields to prevent prototype pollution and unauthorized parameter manipulation.
3. **Engine Response Validation**: Responses received from `AIBO-ENGINE-V1.0` are validated against strict Zod schemas before any proposed actions are presented to the client or executed.

---

## Persistence & Transaction Standards

> [!IMPORTANT]
> **Single Datastore Architecture**: MongoDB is the sole authoritative datastore for all domain entities. PostgreSQL has been completely retired.

1. **Authoritative Entities**: Users, Sessions, Tasks, Schedules, Projects, ProjectColumns, ProjectMembers, TaskAssignments, Notifications, ActivityLogs.
2. **Multi-Document ACID Transactions**: When creating or updating multiple collections simultaneously (e.g., project creation with default columns, task assignment creation), wrap operations in Mongoose sessions:
   ```typescript
   const session = await mongoose.startSession();
   session.startTransaction();
   try {
     // multi-collection operations
     await session.commitTransaction();
   } catch (error) {
     await session.abortTransaction();
     throw error;
   } finally {
     session.endSession();
   }
   ```
3. **Tenant & Ownership Scoping**: Every write operation must verify that the target entity's `ownerId` or project membership matches the authenticated user ID.

---

## Cognitive Engine Orchestration Standards

1. **Monotonic Timeout Budget**: Outbound requests to `POST /orchestrate` must pass an explicit timeout budget (`deadline_ms` or `timeout_ms`), defaulting to 30,000ms.
2. **Shared Secret Authentication**: Outbound engine calls must include the `X-Engine-Secret` header matching `ENGINE_SECRET`.
3. **High-Risk Action Safeguard**: Destructive actions (task deletion, project archiving, bulk rescheduling) proposed by the engine must generate a cryptographically signed HMAC confirmation token. Direct execution without user confirmation is strictly prohibited.
