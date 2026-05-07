# Auth and Token Policy

## Current Browser Policy

- Access token is short lived and returned in JSON.
- Frontend stores access token in memory only.
- Refresh token is stored in an HttpOnly cookie.
- Refresh token is rotated by the backend.
- Logout revokes the refresh session and clears the cookie.

## Required Backend Controls

- Validate JWT issuer and audience.
- Reject weak production JWT secrets.
- Hash refresh tokens at rest.
- Revoke sessions on logout.
- Detect refresh token reuse where implemented.
- Require trusted browser headers/origins for cookie-backed refresh flows.

## Required Frontend Controls

- Do not store refresh tokens.
- Do not store access tokens in localStorage or sessionStorage.
- Clear local auth state on refresh failure.
- Do not expose tokens in logs or error messages.

## Production Requirements

- HTTPS only.
- Secure cookies.
- Non-wildcard CORS.
- Explicit trusted origins.
- Documented token lifetime values.
