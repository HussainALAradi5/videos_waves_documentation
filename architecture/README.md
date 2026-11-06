# Architecture and API contract

```text
 Next.js web client ─┐                    ┌─ MongoDB (users, videos, videolikes, comments, adminrequests)
                     ├── HTTPS / JSON ──► Express API ┤
 Flutter mobile app ─┘   Bearer JWT       └─ storage/videos (uploaded files, streamed via /media/videos)
```

Both clients call the same API through one configured HTTP client each. Neither talks to the database or the file storage directly.

## Response envelope

```json
{ "success": true, "data": { }, "meta": { "page": 1, "pageSize": 10, "totalItems": 42, "totalPages": 5 } }
{ "success": false, "error": { "code": "VALIDATION_ERROR", "message": "…", "details": [{ "field": "body.email", "message": "…" }], "requestId": "…" } }
```

`meta` appears only on list endpoints. Each `details[].field` is `body.*`, `query.*`, or `params.*`. Both clients map `body.*` details onto form fields.

## Error codes

| Code | HTTP | When |
| --- | --- | --- |
| `VALIDATION_ERROR` | 400 | Invalid body, query, or params; bad id; wrong current password; missing upload file |
| `UNAUTHORIZED` | 401 | Missing, invalid, expired, or revoked token; bad credentials |
| `FORBIDDEN` | 403 | Wrong role, or not the owner |
| `NOT_FOUND` | 404 | Missing or soft-deleted resource; unknown route |
| `CONFLICT` | 409 | Duplicate email or username; pending admin request exists; request already decided; already an admin |
| `PAYLOAD_TOO_LARGE` | 413 | Upload over `UPLOAD_MAX_VIDEO_MB` |
| `UNSUPPORTED_MEDIA_TYPE` | 415 | Upload whose MIME type is not `video/*` |
| `TOO_MANY_REQUESTS` | 429 | Rate limit (auth: 20 per 15 minutes; general: 1000 per 15 minutes) |
| `INTERNAL_ERROR` | 500 | Unexpected failure (logged with its `requestId`) |

## Pagination

Every list accepts `page` (≥ 1, default 1), `pageSize` (1–50, default 10), `sortBy` (from a per-list allowlist), and `sortDirection` (`asc` | `desc`, default `desc`). Sorting adds `_id` as a tie-breaker so pages are stable. Skip, limit, and `countDocuments` run in the database.

| List | Sort fields | Filters |
| --- | --- | --- |
| `GET /videos` | `createdAt`, `likesCount`, `viewsCount`, `title` | `search` (title, case-insensitive), `ownerId` |
| `GET /videos/:videoId/comments` | `createdAt` | — |
| `GET /admin-requests` | `createdAt`, `status` | `status` |
| `GET /users` | `createdAt`, `userName`, `email` | `search` (username or email), `role` |

## Authentication flow

1. `register` or `login` returns `{ accessToken (15m), refreshToken (7d), user }`. The two tokens are signed with different secrets and carry a `type` claim, so one can never be used as the other.
2. Each request sends `Authorization: Bearer <accessToken>`. The `authenticate` middleware verifies the token and reloads `role` and `tokenVersion` from the database. Deactivated users, revoked sessions, and role changes therefore take effect immediately.
3. On a 401, the client calls `POST /auth/refresh` once (shared by parallel requests) and retries the request.
4. `logout`, `changePassword`, and `deactivate` increment `tokenVersion`, which revokes every outstanding token. `changePassword` returns a fresh session for the current device.

## Route map (server ↔ web ↔ mobile)

Endpoint paths are defined once on the server (`constants/common/routes.constants.ts`) and mirrored in `src/config/api/endpoints/*` (web) and `lib/config/api/endpoints/*` (mobile). Routes are not versioned.
