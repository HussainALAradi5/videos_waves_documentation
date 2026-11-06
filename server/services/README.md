# Services

| Area | Service | File |
| --- | --- | --- |
| Auth | [Auth and sessions](users/auth/README.md), [Token](users/auth/token.md) | `services/users/auth/*` |
| Users | [User](users/user/README.md), [Profile and visibility](users/profile/README.md), [Settings](users/settings/README.md) | `services/users/{user,profile,visibility,settings}` |
| Social | [Follow, block, restrict](users/social/README.md) | `services/users/{follow,block,restriction}` |
| Admin | [Admin request](users/admin-request/README.md) | `services/users/admin-request` |
| Media | [Media and storage](media/media/README.md) | `services/media/**` |
| Videos | [Video](videos/video/README.md), [Video like](videos/video-like/README.md), [Comment](videos/comment/README.md) | `services/videos/**` |
| Chat | [Conversations and groups](chat/conversation/README.md), [Messages](chat/message/README.md), [Realtime](chat/realtime/README.md) | `services/chat/**` |
| System | [Migrations](system/migration/README.md) | `services/system/migration` |

Services never receive Express objects. They take DTOs and an `AuthUser { id, role, sessionId }` and throw `AppError` factories, which the error handler maps to HTTP: `badRequest` 400, `unauthorized` 401, `forbidden` 403, `notFound` 404, `conflict` 409. The message keys in the tables below are from `constants/common/messages.constants.ts`.
