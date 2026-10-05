# Backend Developer Setup & Operations (`AIBO-BACKEND`)

This document provides setup, testing, and operational guidance for the `AIBO-BACKEND` service.

---

## 1. Prerequisites

- **Node.js**: v22.0.0+ (Tested on v22.17.0)
- **npm**: v10.0.0+
- **MongoDB**: v6.0+ (Replica Set `rs0` enabled)
- **Redis**: v7.0+

---

## 2. Installation & Running

```bash
cd AIBO-BACKEND

# Install dependencies
npm install

# Copy environment template
cp .env.example .env

# Start in development mode with live TypeScript compilation (tsx)
npm run dev
```

The server listens on `http://localhost:5000`.

---

## 3. Core NPM Scripts

| Script | Command | Purpose |
| :--- | :--- | :--- |
| `npm run dev` | `tsx watch src/server.ts` | Development server with hot module reload. |
| `npm run build` | `rimraf dist && tsc` | Compiles TypeScript into production `dist/` bundle. |
| `npm start` | `node dist/server.js` | Runs compiled production server. |
| `npm test` | `jest --runInBand` | Runs all 31 unit and integration test suites. |
| `npm run test:e2e` | `tsx tests/e2e/runner.ts` | Executes all 92 cross-repository E2E scenarios. |
| `npm run lint` | `eslint .` | Runs ESLint static analysis. |
| `npm run lint:fix` | `eslint . --fix` | Automatically formats and fixes lint issues. |
| `npm run db:clear:data` | `tsx scripts/clear-database.ts --force` | Clears all data collections in test database. |

---

## 4. Key Environment Variables

Configure these in `AIBO-BACKEND/.env`:

```ini
PORT=5000
NODE_ENV=development
MONGO_URI=mongodb://127.0.0.1:27017/aibo_backend?replicaSet=rs0
REDIS_URL=redis://127.0.0.1:6379
JWT_SECRET=your_high_entropy_jwt_secret_min_32_chars
JWT_ACCESS_EXPIRES_IN=15m
JWT_REFRESH_EXPIRES_IN=30d
ENGINE_BASE_URL=http://127.0.0.1:5001
ENGINE_SECRET=development_shared_secret
ORCHESTRATION_MODE=canonical
REQUEST_MAX_TIMEOUT_MS=30000
REQUEST_RESERVE_MS=500
```

---

## 5. Architectural Invariants

1. **Authoritative Persistence**: All domain entities (Users, Tasks, Schedules, Projects, Columns, Notifications) reside in MongoDB. PostgreSQL has been retired.
2. **Atomic Durable Claims**: Confirmation of pending actions uses atomic Mongoose state machine transitions (`AWAITING_CONFIRMATION` -> `EXECUTING`).
3. **Monotonic Timeout Budgets**: Downstream calls to the engine or databases inherit the remaining monotonic request budget.
4. **Sanitized Error Envelopes**: Errors return standard envelopes (`success: false`, `error: { code, message }`) with zero stack or secret leakage.
