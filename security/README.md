# Security

## Controls

| Area | Control | Where |
| --- | --- | --- |
| Passwords | bcrypt (12 rounds by default); `passwordHash` never selected or returned | `utilities/security/password.utility.ts`, `user.model.ts` |
| Tokens | Separate access and refresh secrets (≥ 32 characters), `type` claim, 15-minute access lifetime, revocation through `tokenVersion` | `services/users/auth/token.service.ts` |
| Authentication | Every protected request reloads role and token version | `middlewares/auth/authenticate.middleware.ts` |
| Authorization | `authorize(Role.ADMIN)` on admin routes; `assertCanManage` in services for ownership | routes, `validations/common/ownership.validation.ts` |
| Input | Zod DTOs on body, query, and params; strict update DTOs (no mass assignment); sort allowlists; regex-escaped search; ObjectId validation | `dtos/`, `validations/` |
| Uploads | `video/*` only, size limit, one file, UUID names, sanitized extension; the file is deleted if the request fails | `middlewares/uploads/video-upload.middleware.ts` |
| Media | Streams only active videos; `sendFile` with `root` and `dotfiles: deny` | `controllers/media/video-media` |
| Transport | Helmet headers, CORS limited to `CORS_ORIGINS`, 1 MB JSON limit, rate limits (auth 20 per 15 minutes) | `app.ts`, `middlewares/security/*` |
| Logging | Request ID, method, path, status, and duration only; never bodies, tokens, or passwords | `middlewares/logging/request-logger.middleware.ts` |
| Seeds | Development only; passwords from the environment or generated into an ignored file | `seeds/` |
| Secrets | Only `.env.example` files are committed; `.env` and `.env.local` are ignored in every repository | `.gitignore` |

## Findings fixed during the refactor

| # | Finding in the original code | Location (old) | Fix |
| --- | --- | --- | --- |
| 1 | `updateVideo` ran `Object.assign(video, req.body)`, so an owner could set `likesCount`, `userId`, and other fields (mass assignment) | `server/controllers/videos.js` | Strict `updateVideoSchema` (title and description only); tested |
| 2 | `getCommentsByVideo` read `req.params.videoId` on a `/:id` route, and `addComment` saved `id` instead of `videoId`, so comments were never linked to videos | `server/controllers/comments.js` | Typed params DTOs; comments looked up by video and comment id together |
| 3 | Comment edit and delete did not check that the comment belonged to the video in the URL | `server/controllers/comments.js` | `findInVideo(videoId, commentId)`; tested (404) |
| 4 | Video upload accepted any file type and size, and kept the client's extension | `server/routes/video.js` | MIME filter, size limit, UUID names, sanitized extension; tested (415, 413) |
| 5 | Static serving pointed at `client/public/videos`, and `videoUrl` hardcoded `http://localhost:5000` | `server/server.js`, `controllers/videos.js` | Media route from configured storage; URL built from `PUBLIC_BASE_URL` |
| 6 | `cors()` allowed every origin | `server/server.js` | Origin allowlist from the environment |
| 7 | The like toggle was not idempotent and responded before saving | `server/controllers/videos.js` | `VideoLike` unique index plus conditional `$inc`; tested |
| 8 | Admin request list and detail were readable by any signed-in user; any user could accept requests | `server/routes/user.js`, `controllers/adminRequests.js` | Scoped list, owner-or-admin read, admin-only decision; tested |
| 9 | `video.remove()` and `request.remove()` do not exist in Mongoose 8, so deletes always failed with 500 | `controllers/videos.js`, `controllers/adminRequests.js` | Soft delete through `BaseRepository` |
| 10 | Unbounded `Video.find()` despite the client sending `page` and `limit` | `controllers/videos.js` | Server-side pagination (max 50) |
| 11 | The `GET /random` route was registered after `GET /:id`, so it could never be reached | `server/routes/video.js` | Removed (no client used it) |
| 12 | Single long-lived JWT, no refresh and no revocation | `server/service/auth.js` | Access and refresh tokens with `tokenVersion` revocation |
| 13 | Errors leaked as varied shapes (`message`, `error`, `massege`) | all controllers | One error envelope with codes |

## Known limits and follow-ups

- **Web token storage:** tokens are in `localStorage`, so an XSS bug could read them. React escapes output and no raw HTML is rendered. Moving the refresh token to an `httpOnly` cookie needs a same-site deployment and CSRF protection.
- **Admin promotion:** it is two writes (accept the request, then set the role) without a transaction. If the role update fails, the request is marked accepted but the user is not promoted. Re-running the update is safe.
- **Deleted video files** stay on disk. A cleanup job could remove them after a retention period.
- **Release builds** must use HTTPS. Android blocks cleartext HTTP in release builds by default.
