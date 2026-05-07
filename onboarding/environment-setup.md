# Environment Setup

## Backend

Important variables include:

```env
NODE_ENV=development
PORT=5000
MONGO_URI=mongodb://127.0.0.1:27017/aibo_backend
POSTGRES_URL=postgresql://postgres:postgres@127.0.0.1:5432/aibo_backend
JWT_SECRET=replace_with_local_long_random_secret
ENGINE_BASE_URL=http://localhost:5001
ENGINE_TIMEOUT=3000
CORS_ORIGIN=http://localhost:5173
AUTH_COOKIE_NAME=aibo_refresh_token
AUTH_COOKIE_PATH=/api/v1/auth
AUTH_COOKIE_SAME_SITE=lax
AUTH_COOKIE_SECURE=false
```

Use safe local values only. Do not commit real `.env` files.

## Frontend

```env
VITE_APP_NAME=AIBO
VITE_API_BASE_URL=/api/v1
```

Use `.env.local` for local overrides.

## Engine

The engine currently does not include a dependency manifest in the inspected workspace. Install required Python dependencies locally until a manifest is added.

Known runtime libraries include FastAPI, Pydantic, and Uvicorn for the transport.
