# Phase 04: Server logic

**Status:** Done
**Depends on:** Phase 03

## Goal
The REST API: DTOs, validations, repositories, services, controllers, and routes.

## Scope
| Layer | Folders | Items |
| --- | --- | --- |
| DTOs | `src/dtos/` | Zod request schemas, response DTO interfaces |
| Repositories | `src/repositories/` | `BaseRepository` (audit fields, soft delete, pagination) + one per model |
| Services | `src/services/` | Auth, token, user, admin request, video, video like, comment |
| Controllers / routes | `src/controllers/`, `src/routes/` | Thin handlers; routes declare auth, roles, and validation |

## Acceptance criteria
- Every endpoint returns the documented DTOs and error codes ([controllers](../../server/controllers/README.md)).
- Ownership, roles, and workflow transitions are enforced in services.

## Verification
| Check | Command | Result |
| --- | --- | --- |
| Build | `npm run build` | Passed |
| Tests | `npm test` | 7 files, 35 tests passed |
| Runtime | `npm run dev:memory` + curl and browser flows | Health, register, login, upload, stream (206 range), like, comment, admin accept verified |
