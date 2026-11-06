# Globals: config, middlewares, utilities, conventions

## Config (`src/config`)

| File | Purpose |
| --- | --- |
| `index.ts` | Zod-validated environment loader; throws at startup on a missing or invalid value |
| `database/database.config.ts` | `connectDatabase(uri?)`, `disconnectDatabase()` |
| `auth/jwt.config.ts` | Secrets, lifetimes, bcrypt rounds |
| `security/cors.config.ts` | Allows only `CORS_ORIGINS` (requests without an `Origin` header, like the mobile app or curl, are allowed) |
| `security/rate-limit.config.ts` | General and auth limits (relaxed only when `NODE_ENV=test`) |
| `storage/storage.config.ts` | Video folder, max bytes, public base URL |

## Middlewares (`src/middlewares`), in app order

| Middleware | Purpose |
| --- | --- |
| `request/request-id` | Adds `X-Request-Id` (accepts an incoming id of ≤ 100 characters) |
| `logging/request-logger` | JSON log of method, path, status, and duration. Never logs bodies or tokens |
| `security/security-headers` | Helmet; `Cross-Origin-Resource-Policy: cross-origin` so clients can play media |
| CORS, compression, `security/rate-limit` (general), `express.json` (1 MB) | — |
| `auth/authenticate` | `authenticate` and `optionalAuthenticate`: verifies the access token and reloads role and token version |
| `auth/authorize` | `authorize(...roles)` |
| `request/validate` | Parses `body`, `query`, and `params` with DTO schemas into `req.validated` |
| `uploads/video-upload` | Multer disk storage: UUID file names, `video/*` only, size limit, one file, and a required file |
| `errors/not-found`, `errors/error-handler` | Maps `AppError`, Multer, duplicate key (11000), cast, validation, and JSON parse errors to the envelope; deletes the uploaded file on failure; logs 5xx |

## Utilities (`src/utilities`)

`responses/response` (`sendSuccess`, `sendCreated`, `sendPage`, `sendError`), `errors/app-error`, `pagination/paginate` (`toSkip`, `toSort`, `buildPageMeta`), `codes/code-generator`, `security/password`, `media/media-url`, `ids/object-id`, `strings/escape-regex`, `requests/request` (`getBody`, `getQuery`, `getParams`, `requireUser`), and `logging/logger`.

## Repositories (`src/repositories`)

`BaseRepository<T>` provides `create(data, actorId)`, `findById`, `exists`, `updateById` (sets `updatedBy`), `softDeleteById`, and `paginate<TResult>(filter, request, { projection, populate })`. Every read excludes soft-deleted rows. Domain repositories add only queries specific to their model.

## Conventions

- Layer-first folders, with the same `users/…` and `videos/…` subfolders in every layer.
- One exported unit per file; names follow the layer (`video.service.ts`, `create-video.dto.ts`).
- No magic strings: messages, limits, and routes come from `constants/`.
- Routes are not versioned (no `/api/v1`).
