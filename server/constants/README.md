# Constants

| File | Contents |
| --- | --- |
| `constants/common/limits.constants.ts` | Pagination (default 10, max 50), rate limits (15-minute window: 1000 general, 20 auth), JSON body limit 1 MB, code length 8, search ≤ 100 characters |
| `constants/common/routes.constants.ts` | Every route path (the single source of truth that clients mirror) |
| `constants/common/messages.constants.ts` | Every user-facing error and success message |
| `constants/common/regex.constants.ts` | ObjectId, username, password, regex-escape patterns |
| `constants/common/http-status.constants.ts` | `ErrorCode` → HTTP status map |
| `constants/users/user/user.constants.ts` | `USR` prefix, sortable fields, public projection `userName image` |
| `constants/users/user/seed-users.constants.ts` | Seed accounts (one per role) and the credentials file name |
| `constants/users/admin-request/admin-request.constants.ts` | `ADR` prefix, sort fields, reason 10–500, note ≤ 500 |
| `constants/videos/video/video.constants.ts` | `VID`/`LIK` prefixes, sort fields, title 1–120, description ≤ 2000, upload field `file`, MIME prefix `video/` |
| `constants/videos/comment/comment.constants.ts` | `CMT` prefix, sort fields, content 1–1000 |
