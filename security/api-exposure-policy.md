# API Exposure Policy

## Public API Rules

- Every exposed route must have validation.
- Every user-data route must have authentication.
- Authorization checks must happen before resource exposure.
- Rate limits should protect auth and write-heavy routes.
- CORS origins must be explicit in production.
- API errors must not reveal secrets or unnecessary resource existence.

## Engine Exposure

The engine transport is intended for service-to-service use. Do not expose it directly to browsers or public clients without:

- authentication design
- rate limiting
- abuse controls
- input limits
- logging and redaction review
- operational owner

## Documentation Rule

Do not document an endpoint as externally supported until auth, validation, error behavior, and ownership are defined.
