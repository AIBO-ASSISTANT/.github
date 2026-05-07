# Debugging Guide

## Request Debugging

1. Capture the `requestId` from the API response.
2. Search backend logs for the same request ID.
3. Confirm route, status code, validation details, and dependency failures.
4. For auth issues, inspect cookie configuration and trusted origin settings.

## Auth Debugging

Check:

- frontend sends credentials
- backend CORS allows the origin
- `X-AIBO-Client: web` is present for browser refresh/logout flows
- refresh cookie path and same-site settings match environment
- access token is stored only in memory

## Engine Debugging

Check:

- engine service is running on `ENGINE_BASE_URL`
- backend timeout is reasonable for local machine
- engine response contains `intent`, `confidence`, and `processed_input`
- malformed inputs produce validation errors

## Database Debugging

Check:

- MongoDB connection string
- PostgreSQL connection string
- PostgreSQL migrations
- authorization scope before assuming missing data
