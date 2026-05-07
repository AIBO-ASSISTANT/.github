# Backend Setup

From `AIBO-BACKEND`:

```bash
npm install
npm run migrate:postgres
npm run dev
```

## Useful Commands

```bash
npm run lint
npm run test
npm run test:coverage
npm start
```

## Dependencies

Backend requires:

- MongoDB for users, sessions, tasks, and schedules.
- PostgreSQL for project management.
- Engine service for live intent classification.

## Notes

- Production MongoDB scheduling behavior requires transaction-capable deployment.
- Local development can continue with standalone MongoDB behavior supported by backend safeguards.
- `POSTGRES_URL` must be configured before project routes are usable.
