# Constants

| File | Contents |
| --- | --- |
| `constants/common/limits.constants.ts` | Pagination (default 10, max 50, cursor default 30); rate limits per 15 minutes (general 1000, auth 20, refresh 300, upload 120); JSON body 1 MB; code length 8; search ≤ 100 characters |
| `constants/common/routes.constants.ts` | Every route path (the single source of truth that clients mirror) |
| `constants/common/messages.constants.ts` | Every user-facing error and success message |
| `constants/common/regex.constants.ts` | ObjectId, username, password, regex-escape patterns |
| `constants/common/http-status.constants.ts` | `ErrorCode` → HTTP status |
| `constants/users/user/user.constants.ts` | `USR` prefix, email ≤ 254, avatar URL ≤ 2048, bio ≤ 150, public projection |
| `constants/users/user/seed-users.constants.ts` | Seed accounts (`admin`, female; `viewer`, male) |
| `constants/users/session/session.constants.ts` | `SES` prefix, user agent ≤ 200, refresh rotation grace 30 seconds |
| `constants/users/settings/*` | Default settings |
| `constants/users/admin-request/admin-request.constants.ts` | `ADR` prefix, reason 10–500, note ≤ 500 |
| `constants/media/media/media.constants.ts` | `MED` prefix, folder root `/videos-waves`, ImageKit upload URL and thumbnail suffix, TTLs (signature 30 min, signed URL 1 h, pending 24 h), per-kind rules |
| `constants/videos/video/video.constants.ts` | `VID`/`LIK` prefixes, title 1–120, description ≤ 2000 |
| `constants/videos/comment/comment.constants.ts` | `CMT` prefix, content 1–1000, undo window 15 seconds |
| `constants/chat/conversation/conversation.constants.ts` | Title 1–60, group ≤ 32 people, at least 2 invitees, mute ≤ 1 week |
| `constants/chat/message/message.constants.ts` | `MSG`/`RCT` prefixes, body ≤ 2000, ≤ 10 attachments, edit window 15 minutes, preview ≤ 80, reaction set, inbox previews for non-text messages |
