# Services

| Service | File |
| --- | --- |
| [Token](users/auth/token.md) | `services/users/auth/token.service.ts` |
| [Auth](users/auth/README.md) | `services/users/auth/auth.service.ts` |
| [User](users/user/README.md) | `services/users/user/user.service.ts` |
| [Admin request](users/admin-request/README.md) | `services/users/admin-request/admin-request.service.ts` |
| [Video](videos/video/README.md) | `services/videos/video/video.service.ts` |
| [Video like](videos/video-like/README.md) | `services/videos/video-like/video-like.service.ts` |
| [Comment](videos/comment/README.md) | `services/videos/comment/comment.service.ts` |

Services never receive Express objects. They take DTOs and an `AuthUser { id, role }`, and they throw `AppError`, which the error-handler middleware maps to HTTP. Exception names below are the `AppError` factories: `badRequest`, `unauthorized`, `forbidden`, `notFound`, `conflict`.
