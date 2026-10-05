# AIBO Assistant — Database Ownership & Data Architecture

This document defines the storage layers, data domain ownership, indexing strategies, and transactional boundaries for the AIBO Assistant.

---

## 1. Storage Layer Division

| Storage Layer | Engine / Technology | Access Authority | Primary Roles |
| :--- | :--- | :--- | :--- |
| **Primary Persistence** | MongoDB 6+ (`replicaSet=rs0`) | `AIBO-BACKEND` only | Canonical source of truth for all persistent entities, multi-document transactions, and durable confirmation state. |
| **High-Throughput Cache** | Redis 7+ Alpine | `AIBO-BACKEND` only | BullMQ background job queues, distributed rate-limit counters, real-time socket pub/sub coordination. |
| **Cognitive Memory Cache**| In-process & Backend MongoDB | `AIBO-ENGINE-V1.0` (via Backend API) | Ephemeral conversation turn memory synced asynchronously to backend storage. |

> **Architectural Decision Note**: Early prototypes explored PostgreSQL for project management. In V1.0, all entity models have been consolidated into MongoDB using Mongoose schemas. This eliminates distributed transaction complexity, provides unified document modeling, and leverages MongoDB replica set multi-document ACID transactions.

---

## 2. MongoDB Domain Entity Ownership

All collections are owned exclusively by `AIBO-BACKEND`. No other component has direct database connection credentials.

| Entity Domain | Collection Name | Authoritative Model | Key Fields & Indexes |
| :--- | :--- | :--- | :--- |
| **Users** | `users` | `src/modules/users/user.model.ts` | `email` (unique, lowercase), `password` (bcrypt hash), `name`, `avatar`, `role`, `preferences`. |
| **Sessions** | `sessions` | `src/modules/auth/session.model.ts` | `userId`, `refreshTokenHash`, `expiresAt`, `revokedAt`, `userAgent`, `ipAddress`. TTL index on `expiresAt`. |
| **Tasks** | `tasks` | `src/modules/tasks/task.model.ts` | `userId`, `projectId`, `columnId`, `title`, `status` (`pending`, `completed`), `priority`, `dueDate`, `isDeleted` (soft-delete). |
| **Schedules** | `schedules` | `src/modules/schedules/schedule.model.ts` | `userId`, `title`, `date`, `startTime`, `endTime`, `status` (`scheduled`, `completed`, `cancelled`), `conflictCheck`. |
| **Projects** | `projects` | `src/modules/projects/project.model.ts` | `ownerId`, `name`, `description`, `color`, `members`, `columns`, `isArchived`. |
| **Columns** | `project_columns` | `src/modules/projects/column.model.ts` | `projectId`, `title`, `order`, `color`, `wipLimit`. Compound index on `(projectId, order)`. |
| **Notifications** | `notifications` | `src/modules/notifications/notification.model.ts` | `userId`, `title`, `message`, `type`, `read`, `category`, `quietHoursSuppressed`, `metadata`. |
| **Durable State** | `orchestration_state` | `src/modules/orchestration-state/state.model.ts` | `stateRecordId` (unique), `userId`, `confirmationToken` (HMAC), `status` (`AWAITING`, `EXECUTING`, `COMPLETED`), `authorizedActions`. |
| **Cognitive Memory** | `cognitive_memories`| `src/modules/memory/memory.model.ts` | `userId`, `category` (`semantic`, `working`), `key`, `value`, `confidence`. |

---

## 3. Storage Rules & Invariants

1. **Zero Direct Client or Engine DB Access**: `AIBO-FRONTEND` and `AIBO-ENGINE-V1.0` possess zero database driver dependencies or connection strings. All reads and mutations flow through validated HTTP/REST endpoints in `AIBO-BACKEND`.
2. **Soft Deletion Policy**: Critical user entities (Tasks, Projects, Schedules) utilize soft-deletion flags (`isDeleted: true`) with timestamps, preserving historic referential integrity and audit trails.
3. **Atomic Confirmation Claiming**: The transition of a durable confirmation record from `AWAITING_CONFIRMATION` to `EXECUTING` is executed via atomic `findOneAndUpdate` with pre-condition matching. If two requests attempt to confirm simultaneously, exactly one succeeds; the second receives an idempotent replay rejection.
4. **Zero Mutation on Request Failure**: If an orchestration pipeline aborts due to client timeout, validation error, or provider outage, all database mutations abort with zero database state alterations.
