# Phase 02: Foundation

**Status:** Done
**Depends on:** Phase 01

## Goal
Build the shared pieces every server layer uses.

## Scope
| Layer | Folders | Items |
| --- | --- | --- |
| Config | `src/config/` | Zod environment loader (fails fast), database, JWT, CORS, rate limits, storage |
| Constants | `src/constants/` | Messages, limits, routes, regex, per-domain sort fields and limits |
| Enums | `src/enums/` | `Role`, `AdminRequestStatus`, `ErrorCode`, `SortDirection`, `TokenType` |
| Utilities | `src/utilities/` | Response envelope, `AppError`, pagination, code generator, passwords, media URLs, regex escaping |
| Middlewares | `src/middlewares/` | Request ID, logging, security headers, rate limit, validation, auth, error handler, uploads |

## Acceptance criteria
- The server refuses to start when `MONGO_URI` or a JWT secret is missing or too short.
- Every response uses `{ success, data, meta? }` or `{ success: false, error }`.

## Verification
| Check | Command | Result |
| --- | --- | --- |
| Typecheck | `npm run typecheck` | Passed |
| Utility tests | `npm test` (pagination, code generator, pagination validation) | Passed |
