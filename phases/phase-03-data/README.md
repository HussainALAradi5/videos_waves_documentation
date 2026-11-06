# Phase 03: Data

**Status:** Done
**Depends on:** Phase 02

## Goal
Persisted models with the base entity, relationships, indexes, and a development seed.

## Scope
| Layer | Folders | Items |
| --- | --- | --- |
| Models | `src/models/` | `baseEntityPlugin`, `User`, `Video`, `VideoLike`, `Comment`, `AdminRequest` |
| Seeds | `src/seeds/` | One user per role (`admin`, `viewer`); refuses production |

## Migrations
MongoDB has no schema migrations. Indexes are declared on the schemas and built by Mongoose (`syncIndexes` in tests). Data from the original monorepo is **not** migrated: field names changed (`password_digest` → `passwordHash`, `isAdmin` → `role`, `likedBy` → `VideoLike`, comment `comment` → `content`, `userId` → `owner`/`author`/`requester`). A migration script is a listed follow-up if old data must be kept.

## Acceptance criteria
- `code` and `id` are unique at the database level.
- The seed creates exactly one account per role and is idempotent.

## Verification
| Check | Command | Result |
| --- | --- | --- |
| Seed on empty database | `npm run dev:memory` | Admin and viewer seeded; credentials file written |
| Unique and partial indexes | HTTP tests (one pending admin request per user, one like per user per video) | Passed |
