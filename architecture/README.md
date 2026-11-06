# Architecture and API contract

```text
 Next.js web client ─┐  HTTPS / JSON (Bearer JWT)   ┌─ MongoDB replica set (transactions)
                     ├──────────────────────────────► Express API ┤
 Flutter mobile app ─┤  WebSocket (Socket.IO, JWT)   └─ ImageKit API (signatures, file checks, deletes)
                     │
                     └── direct upload + delivery ──► ImageKit CDN (videos, thumbnails, avatars, chat files)
```

Both clients call the API through one configured HTTP client and keep one socket per signed-in device. **File bytes never pass through the API** in production: clients upload to ImageKit with a server-issued signature and the API checks the file afterwards (see [integrations](../integrations/README.md)). With `MEDIA_PROVIDER=local`, the API itself plays ImageKit's role for offline development.

## Response envelope

```json
{ "success": true, "data": { }, "meta": { "page": 1, "pageSize": 10, "totalItems": 42, "totalPages": 5 } }
{ "success": true, "data": [ ], "meta": { "nextCursor": "6745…" } }
{ "success": false, "error": { "code": "VALIDATION_ERROR", "message": "…", "details": [{ "field": "body.email", "message": "…" }], "requestId": "…" } }
```

`meta` appears only on list endpoints: page meta for paged lists, `nextCursor` for chat messages. Each `details[].field` is `body.*`, `query.*`, or `params.*`; both clients map `body.*` onto form fields.

## Error codes

| Code | HTTP | When |
| --- | --- | --- |
| `VALIDATION_ERROR` | 400 | Invalid body, query, or params; bad id; wrong current password; following yourself; group rules |
| `UNAUTHORIZED` | 401 | Missing, invalid, expired, or revoked token; reused refresh token; bad credentials; Following feed as a guest |
| `FORBIDDEN` | 403 | Wrong role; not the owner; not a member; blocked; replying to a pending request |
| `NOT_FOUND` | 404 | Missing or soft-deleted resource; a user who blocked you; unknown route |
| `CONFLICT` | 409 | Duplicate identity; admin request rules; media already used; edit window passed; undo expired; last admin; group full |
| `PAYLOAD_TOO_LARGE` | 413 | Uploaded file over the kind's limit |
| `UNSUPPORTED_MEDIA_TYPE` | 415 | Uploaded file type not allowed for the kind |
| `TOO_MANY_REQUESTS` | 429 | Rate limit (per 15 minutes: general 1000, auth 20, refresh 300, uploads 120) |
| `INTERNAL_ERROR` | 500 | Unexpected failure (logged with its `requestId`) |

## Pagination

**Page lists** accept `page` (≥ 1, default 1), `pageSize` (1–50, default 10), `sortBy` (per-list allowlist), and `sortDirection` (`asc` | `desc`). Sorting adds `_id` as a tie-breaker so pages are stable; skip, limit, and count run in the database.

| List | Filters |
| --- | --- |
| `GET /videos` | `search` (title), `ownerId`, `scope` (`all` \| `following`) |
| `GET /videos/:videoId/comments`, `…/replies` | — |
| `GET /users/search`, `…/followers`, `…/following`, `/users/me/blocked`, `/users/me/restricted` | `search` (search only) |
| `GET /chat/conversations` | `folder` (`inbox`, `requests`, `groups`, `unread`) |
| `GET /admin-requests`, `GET /users` | `status`; `search`, `role` |

**Cursor lists** (`GET /chat/conversations/:id/messages`) take `pageSize` (default 30) and `before=<messageId>`, newest first, and return `meta.nextCursor` (null at the start of the chat). New messages arrive over the socket instead of shifting pages.

Clients share one hook or controller per style: web `usePagedList` / `useCursorList`, mobile `PagedListController<T>` / `ThreadController`.

## Sessions

1. `register` or `login` creates a **Session** (one per device) and returns `{ accessToken (15m), refreshToken (7d), user }`. The tokens use different secrets and a `type` claim; both carry the session id `sid`, and the refresh token's `jti` must match the session's current token.
2. Each request sends `Authorization: Bearer <accessToken>`. `resolveAccessToken` reloads role, `tokenVersion`, and the session, so deactivation, revoked devices, and role changes apply at once.
3. On a 401 the client refreshes once and retries. `refreshSession` **rotates** the refresh token. A token rotated within the last 30 seconds still works once (two tabs refreshing together); any older token means it was copied, and the session is revoked.
4. The web client serialises refreshes across tabs with the Web Locks API and signs out only when the refresh itself returns 401, never on a network error. The mobile app keeps its tokens when offline and clears them only on a rejected refresh.
5. `POST /auth/logout` signs out **this device**; `POST /auth/logout-all`, a password change, and deactivation sign out every device. `GET /auth/sessions` lists devices and `DELETE /auth/sessions/:id` signs one out.

## Realtime

One Socket.IO connection per device, authenticated with the access token. Each user has a room `user:<id>`; services emit only after their transaction commits. Events are listed in [server → realtime](../server/realtime/README.md).

## Route map (server ↔ web ↔ mobile)

Paths are defined once on the server (`constants/common/routes.constants.ts`) and mirrored in `src/config/api/endpoints/*` (web) and `lib/config/api/endpoints/*` (mobile). Routes are not versioned.
