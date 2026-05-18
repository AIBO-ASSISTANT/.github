# Local Setup

The workspace is expected to contain sibling repositories:

```text
AIBO_ASSISTANT/
  .github/
  AIBO-BACKEND/
  AIBO-FRONTEND/
  AIBO-ENGINE/
```

## Required Local Tools

- Node.js 22 or newer for backend.
- Node.js compatible with the frontend package lock.
- npm.
- Python 3 with FastAPI/Pydantic available for engine transport.
- MongoDB.
- PostgreSQL.
- Git.

## Suggested Startup Order

1. Start MongoDB.
2. Start PostgreSQL.
3. Start the engine if testing intent classification.
4. Start backend.
5. Start frontend.

## Validate Setup

- Backend health route responds.
- Frontend dev server loads.
- Engine `/ai-engine/analyze` and `/decision-engine/decide` respond when running.
- Auth login/signup flow works against the backend.
