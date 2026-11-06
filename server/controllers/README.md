# Controllers

"Any" = public; "Signed in" = `authenticate`; "Optional" = `optionalAuthenticate` (personalizes output when a valid token is sent; an invalid token still gets 401). Every endpoint can also return 400 for invalid input, 429 when rate-limited, and 500.

## AuthController (`controllers/users/auth`, base `/auth`, rate-limited 20 per 15 minutes)

| Method and path | Service function | Why it is used | Role | Request DTO | Response DTO | Exceptions |
| --- | --- | --- | --- | --- | --- | --- |
| `POST /auth/register` | `authService.register` | Create an account and sign in | Any | `RegisterDto` | `AuthResponseDto` (201) | 400, 409 |
| `POST /auth/login` | `authService.login` | Sign in with a username or email | Any | `LoginDto` | `AuthResponseDto` | 400, 401 |
| `POST /auth/refresh` | `authService.refresh` | Renew an expired access token | Any | `RefreshTokenDto` | `AuthTokensDto` | 400, 401 |
| `POST /auth/logout` | `authService.logout` | Revoke all sessions | Signed in | — | `{ message }` | 401 |

## UserController (`controllers/users/user`, base `/users`, all signed in)

| Method and path | Service function | Why it is used | Role | Request DTO | Response DTO | Exceptions |
| --- | --- | --- | --- | --- | --- | --- |
| `GET /users` | `userService.listUsers` | Admin user directory | admin | `UserQueryDto` | `UserResponseDto[]` + meta | 401, 403 |
| `GET /users/me` | `userService.getProfile` | Current user | Signed in | — | `UserResponseDto` | 401, 404 |
| `PATCH /users/me` | `userService.updateProfile` | Edit username, email, or avatar | Signed in | `UpdateProfileDto` | `UserResponseDto` | 400, 401, 409 |
| `PATCH /users/me/password` | `userService.changePassword` | Change password (rate-limited) | Signed in | `ChangePasswordDto` | `AuthResponseDto` | 400, 401 |
| `DELETE /users/me` | `userService.deactivateAccount` | Deactivate account | Signed in | — | `{ message }` | 401, 404 |

## VideoController (`controllers/videos/video`, base `/videos`)

| Method and path | Service function | Why it is used | Role | Request DTO | Response DTO | Exceptions |
| --- | --- | --- | --- | --- | --- | --- |
| `GET /videos` | `videoService.listVideos` | Feed, search, and "my videos" | Optional | `VideoQueryDto` | `VideoResponseDto[]` + meta | 400 |
| `POST /videos` | `videoService.createVideo` | Upload (multipart `file`, `title`, `description`) | Signed in | `CreateVideoDto` + file | `VideoResponseDto` (201) | 400, 401, 413, 415 |
| `GET /videos/:videoId` | `videoService.getVideo` | Video page | Optional | `VideoParamsDto` | `VideoResponseDto` | 400, 404 |
| `PATCH /videos/:videoId` | `videoService.updateVideo` | Edit title or description | Owner or admin | `UpdateVideoDto` | `VideoResponseDto` | 400, 401, 403, 404 |
| `DELETE /videos/:videoId` | `videoService.deleteVideo` | Remove a video | Owner or admin | `VideoParamsDto` | `{ message }` | 401, 403, 404 |
| `POST /videos/:videoId/views` | `videoService.recordView` | Count a view on first play | Any | `VideoParamsDto` | `{ videoId }` | 404 |
| `PUT /videos/:videoId/like` | `videoLikeService.likeVideo` | Like (idempotent) | Signed in | `VideoParamsDto` | `VideoLikeResponseDto` | 401, 404 |
| `DELETE /videos/:videoId/like` | `videoLikeService.unlikeVideo` | Unlike (idempotent) | Signed in | `VideoParamsDto` | `VideoLikeResponseDto` | 401, 404 |

## CommentController (`controllers/videos/comment`, base `/videos/:videoId/comments`)

| Method and path | Service function | Why it is used | Role | Request DTO | Response DTO | Exceptions |
| --- | --- | --- | --- | --- | --- | --- |
| `GET /videos/:videoId/comments` | `commentService.listComments` | Paged comments | Any | `CommentQueryDto` | `CommentResponseDto[]` + meta | 400, 404 |
| `POST /videos/:videoId/comments` | `commentService.createComment` | Add a comment | Signed in | `CommentBodyDto` | `CommentResponseDto` (201) | 400, 401, 404 |
| `PATCH /videos/:videoId/comments/:commentId` | `commentService.updateComment` | Edit own comment | Author | `CommentBodyDto` | `CommentResponseDto` | 400, 401, 403, 404 |
| `DELETE /videos/:videoId/comments/:commentId` | `commentService.deleteComment` | Remove a comment | Author or admin | `CommentParamsDto` | `{ message }` | 401, 403, 404 |

## AdminRequestController (`controllers/users/admin-request`, base `/admin-requests`, all signed in)

| Method and path | Service function | Why it is used | Role | Request DTO | Response DTO | Exceptions |
| --- | --- | --- | --- | --- | --- | --- |
| `POST /admin-requests` | `createAdminRequest` | Ask for admin access | user | `CreateAdminRequestDto` | `AdminRequestResponseDto` (201) | 400, 401, 409 |
| `GET /admin-requests` | `listAdminRequests` | Own requests, or all for admins | Signed in | `AdminRequestQueryDto` | `AdminRequestResponseDto[]` + meta | 400, 401 |
| `GET /admin-requests/:requestId` | `getAdminRequest` | One request | Requester or admin | `AdminRequestParamsDto` | `AdminRequestResponseDto` | 401, 403, 404 |
| `PATCH /admin-requests/:requestId/decision` | `decideAdminRequest` | Accept or reject | admin | `DecideAdminRequestDto` | `AdminRequestResponseDto` | 400, 401, 403, 404, 409 |
| `DELETE /admin-requests/:requestId` | `cancelAdminRequest` | Withdraw a pending request | Requester | `AdminRequestParamsDto` | `AdminRequestResponseDto` | 401, 403, 404, 409 |

## Media and health (`routes/index.ts`)

| Method and path | Handler | Why it is used | Role | Exceptions |
| --- | --- | --- | --- | --- |
| `GET /media/videos/:fileName` | `streamVideo` → `videoService.isStreamable` | Streams files with HTTP Range (206); refuses files of deleted videos; `sendFile` with `root` blocks path traversal | Any | 404 |
| `GET /health` | inline | Liveness check | Any | — |
