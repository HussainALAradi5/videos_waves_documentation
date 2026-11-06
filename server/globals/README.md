# Globals: config, middlewares, utilities, repositories, migrations

## Config (`src/config`)

| File | Purpose |
| --- | --- |
| `index.ts` | Zod-validated environment; throws at startup on a missing or invalid value |
| `database/database.config.ts` | `connectDatabase`, `disconnectDatabase`; enables `transactionAsyncLocalStorage` so repositories join the current transaction automatically |
| `auth/jwt.config.ts` | Secrets, lifetimes, bcrypt rounds |
| `security/cors.config.ts` | Allows only `CORS_ORIGINS` (no-`Origin` requests such as the mobile app are allowed) |
| `security/rate-limit.config.ts` | General, auth, refresh, and upload limits (relaxed only when `NODE_ENV=test`) |
| `storage/storage.config.ts` | Media provider, ImageKit keys (fail fast when missing), local folder and secret, `UPLOAD_MAX_VIDEO_MB` |
| `realtime/socket.config.ts` | Socket.IO server, CORS, auth middleware, connection handler |

### Environment

| Variable | Default | Notes |
| --- | --- | --- |
| `MONGO_URI` | — | Must be a replica set (transactions) |
| `JWT_ACCESS_SECRET`, `JWT_REFRESH_SECRET` | — | ≥ 32 characters each, different |
| `JWT_ACCESS_EXPIRES_IN`, `JWT_REFRESH_EXPIRES_IN` | `15m`, `7d` | |
| `CORS_ORIGINS` | — | Comma-separated browser origins |
| `PUBLIC_BASE_URL` | — | Builds local media URLs |
| `MEDIA_PROVIDER` | `local` | `imagekit` for real deployments |
| `IMAGEKIT_PUBLIC_KEY`, `IMAGEKIT_PRIVATE_KEY`, `IMAGEKIT_URL_ENDPOINT` | — | Required when the provider is `imagekit` |
| `MEDIA_LOCAL_SECRET`, `MEDIA_LOCAL_DIR` | —, `storage/media` | Local provider only |
| `UPLOAD_MAX_VIDEO_MB` | `100` | Capped at 100 (ImageKit free plan) |
| `SEED_ADMIN_PASSWORD`, `SEED_USER_PASSWORD` | — | Generated into `.seed-credentials.local.json` when empty |

## Middlewares (`src/middlewares`), in app order

| Middleware | Purpose |
| --- | --- |
| `request/request-id` | `X-Request-Id` (accepts an incoming id ≤ 100 characters) |
| `logging/request-logger` | Method, path, status, duration; never bodies or tokens |
| `security/security-headers` | Helmet; `Cross-Origin-Resource-Policy: cross-origin` for media |
| CORS, compression, `security/rate-limit`, `express.json` (1 MB) | — |
| `auth/authenticate` | `authenticate`, `optionalAuthenticate` through `resolveAccessToken` |
| `auth/authorize` | `authorize(...roles)` |
| `request/validate` | Parses `body`, `query`, `params` into `req.validated` |
| `uploads/local-media-upload` | Multer for `POST /media/local-upload` only (local provider) |
| `realtime/socket-auth` | Socket handshake authentication |
| `errors/not-found`, `errors/error-handler` | Maps `AppError`, Multer, duplicate key, cast, validation, and JSON errors to the envelope; logs 5xx |

## Utilities (`src/utilities`)

`database/transaction` (`withTransaction`), `responses/response` (`sendSuccess`, `sendCreated`, `sendPage`, `sendCursorPage`), `errors/app-error`, `pagination/paginate` and `pagination/cursor` (`buildCursorPage`), `codes/code-generator`, `security/password`, `media/media-url`, `realtime/room` (`userRoom`), `collections/group-by` and `collections/intersect`, `ids/object-id`, `strings/escape-regex`, `requests/request` (`requireUser`, `userAgentOf`), `logging/logger`.

## Repositories (`src/repositories`)

`BaseRepository<T>`: `create(data, actorId)`, `findById`, `exists`, `updateById` (sets `updatedBy`), `softDeleteById`, `restoreById`, and `paginate(filter, request, { projection, populate })`. Every read excludes soft-deleted rows. `base/pair-relation.repository.ts` is shared by follow, block, and restriction (`addOnce`, `removeOnce`, `exists`, `findTargetIds`, paged lists).

## Migrations

`src/migrations/index.ts` lists them in order; `npm run migrate` applies pending ones (`runPendingMigrations`) and records each in `migrations`.

| Id | What it does |
| --- | --- |
| `001-legacy-schema` | Upgrades data from the original app: `password_digest` → `passwordHash`, `isAdmin` → `role`, `image` → `avatarUrl`, `isActive=false` → soft-deleted; adds `code` and audit fields; links old comments to their videos; maps legacy local video files to `provider: local` media |
| `002-sync-indexes` | Creates every model index |

`npm run media:cleanup` purges CDN files of deleted media that failed earlier and removes stale pending uploads. Run it on a schedule (for example daily).

## Conventions

- Layer-first folders with the same `<area>/<feature>/` subfolders in every layer.
- One exported unit per file; files stay under 150 lines.
- No magic strings: messages, limits, and routes come from `constants/`.
- Routes are not versioned.
