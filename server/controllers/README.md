# Controllers

"Any" = public; "Signed in" = `authenticate`; "Optional" = `optionalAuthenticate` (personalizes output when a valid token is sent; an invalid token still gets 401). Every endpoint can also return 400 for invalid input, 429 when rate-limited, and 500. Error codes are described in the [architecture](../../architecture/README.md#error-codes).

## AuthController (`controllers/users/auth`, base `/auth`)

| Method and path | Service function | Why | Role | Request DTO | Response DTO | Exceptions |
| --- | --- | --- | --- | --- | --- | --- |
| `POST /auth/register` | `authService.register` | Create an account (with gender) and sign in | Any (auth limit) | `RegisterDto` | `AuthResponseDto` (201) | 400, 409 |
| `POST /auth/login` | `authService.login` | Sign in with username or email | Any (auth limit) | `LoginDto` | `AuthResponseDto` | 400, 401 |
| `POST /auth/refresh` | `sessionService.refreshSession` | Rotate tokens | Any (refresh limit) | `RefreshTokenDto` | `AuthTokensDto` | 400, 401 |
| `POST /auth/logout` | `sessionService.revokeSession` | Sign out this device | Signed in | — | `{ message }` | 401 |
| `POST /auth/logout-all` | `sessionService.revokeAllSessions` | Sign out every device | Signed in | — | `{ message }` | 401 |
| `GET /auth/sessions` | `sessionService.listSessions` | Active devices | Signed in | — | `SessionResponseDto[]` | 401 |
| `DELETE /auth/sessions/:sessionId` | `sessionService.revokeSession` | Sign out one device | Signed in | `SessionParamsDto` | `{ message }` | 401, 404 |

## User, profile, settings, and social controllers (base `/users`)

| Method and path | Service function | Why | Role | Request DTO | Response DTO | Exceptions |
| --- | --- | --- | --- | --- | --- | --- |
| `GET /users` | `userService.listUsers` | Admin directory | admin | `UserQueryDto` | `UserResponseDto[]` + meta | 401, 403 |
| `GET /users/search` | `userService.searchUsers` | People search | Optional | `UserSearchQueryDto` | `UserSummaryResponseDto[]` + meta | 400 |
| `GET /users/me` | `userService.getProfile` | Current account | Signed in | — | `UserResponseDto` | 401, 404 |
| `PATCH /users/me` | `userService.updateProfile` | Username, email, bio | Signed in | `UpdateProfileDto` | `UserResponseDto` | 400, 401, 409 |
| `DELETE /users/me` | `userService.deactivateAccount` | Deactivate | Signed in | — | `{ message }` | 401, 404 |
| `PATCH /users/me/avatar` | `userService.updateAvatar` | Set or remove the profile picture | Signed in | `UpdateAvatarDto` | `UserResponseDto` | 400, 401, 409 |
| `PATCH /users/me/password` | `userService.changePassword` | Change password (signs other devices out) | Signed in (auth limit) | `ChangePasswordDto` | `AuthResponseDto` | 400, 401 |
| `GET /users/me/settings` | `settingsService.getSettings` | Read settings | Signed in | — | `SettingsValues` | 401 |
| `PATCH /users/me/settings` | `settingsService.updateSettings` | Change settings by key | Signed in | `UpdateSettingsDto` | `SettingsValues` | 400, 401 |
| `GET /users/me/blocked` | `blockService.listBlockedUsers` | Blocked accounts | Signed in | `RelationQueryDto` | `UserSummaryResponseDto[]` + meta | 401 |
| `GET /users/me/restricted` | `restrictionService.listRestrictedUsers` | Restricted accounts | Signed in | `RelationQueryDto` | `UserSummaryResponseDto[]` + meta | 401 |
| `GET /users/:userName` | `profileService.getProfile` | Profile with relations | Optional | `UserNameParamsDto` | `ProfileResponseDto` | 404 |
| `GET /users/:userName/followers` | `followService.listFollows('followers')` | Followers | Optional | `RelationQueryDto` | `UserSummaryResponseDto[]` + meta | 404 |
| `GET /users/:userName/following` | `followService.listFollows('following')` | Following | Optional | `RelationQueryDto` | `UserSummaryResponseDto[]` + meta | 404 |
| `PUT` / `DELETE /users/:userName/follow` | `followUser` / `unfollowUser` | Follow, unfollow (idempotent) | Signed in | `UserNameParamsDto` | `FollowStateResponseDto` | 400, 401, 404, 409 |
| `PUT` / `DELETE /users/:userName/block` | `blockUser` / `unblockUser` | Block, unblock | Signed in | `UserNameParamsDto` | `RelationStateResponseDto` | 400, 401, 404 |
| `PUT` / `DELETE /users/:userName/restrict` | `restrictUser` / `unrestrictUser` | Restrict, unrestrict | Signed in | `UserNameParamsDto` | `RelationStateResponseDto` | 400, 401, 404 |

## MediaController (`controllers/media/media`, base `/media`)

| Method and path | Service function | Why | Role | Request DTO | Response DTO | Exceptions |
| --- | --- | --- | --- | --- | --- | --- |
| `POST /media/signature` | `mediaService.createUploadSignature` | Step 1: signature for a direct CDN upload | Signed in (upload limit) | `UploadSignatureRequestDto` (`kind`) | `UploadSignatureResponseDto` | 400, 401 |
| `POST /media` | `mediaService.registerMedia` | Step 3: record the uploaded file after checking it | Signed in | `RegisterMediaDto` (`fileId`, `kind`, `originalName`) | `MediaResponseDto` (201) | 400, 401, 404, 409, 413, 415 |
| `DELETE /media/:mediaId` | `mediaService.deletePendingMedia` | Discard an unused upload | Signed in | `MediaParamsDto` | `{ message }` | 401, 404, 409 |
| `POST /media/local-upload` | `localMediaService.saveLocalUpload` | Local provider's stand-in for ImageKit's upload endpoint (multipart) | Signature (upload limit) | `LocalUploadDto` + file | provider-shaped `{ fileId, … }` | 400, 401, 403 |
| `GET /media/files/*filePath` | `localMediaService.resolveLocalFile` | Local delivery; private files need `expires` and `signature` | Any | query | file | 403, 404 |

## VideoController (`controllers/videos/video`, base `/videos`)

| Method and path | Service function | Why | Role | Request DTO | Response DTO | Exceptions |
| --- | --- | --- | --- | --- | --- | --- |
| `GET /videos` | `videoService.listVideos` | Feed (`scope=all|following`), search, one owner's videos | Optional | `VideoQueryDto` | `VideoResponseDto[]` + meta | 400, 401 (following as guest) |
| `POST /videos` | `videoService.createVideo` | Publish an uploaded video | Signed in | `CreateVideoDto` (`mediaId`, `title`, `description`) | `VideoResponseDto` (201) | 400, 401, 409 |
| `GET /videos/:videoId` | `videoService.getVideo` | Video page | Optional | `VideoParamsDto` | `VideoResponseDto` | 404 |
| `PATCH /videos/:videoId` | `videoService.updateVideo` | Edit title or description | Owner or admin | `UpdateVideoDto` | `VideoResponseDto` | 400, 401, 403, 404 |
| `DELETE /videos/:videoId` | `videoService.deleteVideo` | Delete with comments, likes, and CDN file | Owner or admin | `VideoParamsDto` | `{ message }` | 401, 403, 404 |
| `POST /videos/:videoId/views` | `videoService.recordView` | Count a view | Any | `VideoParamsDto` | `{ videoId }` | 404 |
| `PUT` / `DELETE /videos/:videoId/like` | `likeVideo` / `unlikeVideo` | Like, unlike (idempotent) | Signed in | `VideoParamsDto` | `VideoLikeResponseDto` | 401, 404 |

## CommentController (`controllers/videos/comment`, base `/videos/:videoId/comments`)

| Method and path | Service function | Why | Role | Request DTO | Response DTO | Exceptions |
| --- | --- | --- | --- | --- | --- | --- |
| `GET …/comments` | `listComments` | Top-level comments | Optional | `CommentQueryDto` | `CommentResponseDto[]` + meta | 404 |
| `POST …/comments` | `createComment` | Comment or reply (`parentId`) | Signed in | `CreateCommentDto` | `CommentResponseDto` (201) | 400, 401, 404 |
| `GET …/comments/:commentId/replies` | `listReplies` | Replies | Optional | `CommentQueryDto` | `CommentResponseDto[]` + meta | 404 |
| `PATCH …/comments/:commentId` | `updateComment` | Edit own comment | Author | `CommentBodyDto` | `CommentResponseDto` | 400, 401, 403, 404 |
| `DELETE …/comments/:commentId` | `deleteComment` | Delete (with replies) | Author, video owner, or admin | `CommentParamsDto` | `{ message }` | 401, 403, 404 |
| `PATCH …/comments/:commentId/restore` | `restoreComment` | Undo a delete (15 s) | The deleter | `CommentParamsDto` | `CommentResponseDto` | 401, 403, 404, 409 |
| `PATCH …/comments/:commentId/approve` | `approveComment` | Publish a restricted comment | Video owner | `CommentParamsDto` | `CommentResponseDto` | 401, 403, 409 |

## Conversation and message controllers (`controllers/chat/**`, base `/chat`, all signed in)

| Method and path | Service function | Why | Request DTO | Response DTO | Exceptions |
| --- | --- | --- | --- | --- | --- |
| `GET /chat/unread-count` | `getUnreadTotal` | Messages badge | — | `{ unreadCount }` | 401 |
| `GET /chat/conversations` | `listConversations` | Inbox folders, paged | `ConversationQueryDto` (`folder`) | `ConversationResponseDto[]` + meta | 400, 401 |
| `POST /chat/conversations` | `startDirect` | Open a direct chat | `StartDirectDto` (`userName`) | `ConversationResponseDto` | 400, 401, 403, 404 |
| `POST /chat/groups` | `createGroup` | New group | `CreateGroupDto` (`title`, `userNames`) | `ConversationResponseDto` (201) | 400, 401, 403, 404, 409 |
| `GET /chat/conversations/:conversationId` | `getConversation` | Thread header and members | `ConversationParamsDto` | `ConversationResponseDto` | 401, 403, 404 |
| `PATCH /chat/conversations/:conversationId` | `updateConversation` | Rename, mute | `UpdateConversationDto` | `ConversationResponseDto` | 400, 401, 403 |
| `DELETE /chat/conversations/:conversationId` | `hideConversation` | Delete chat for me | — | `{ message }` | 401, 403 |
| `POST …/:conversationId/accept` | `acceptRequest` | Accept a request | — | `ConversationResponseDto` | 401, 403, 409 |
| `POST …/:conversationId/read` | `markConversationRead` | Seen | — | `{ message }` | 401, 403 |
| `POST …/:conversationId/members` | `addMembers` | Add people (admin) | `AddMembersDto` | `ConversationResponseDto` | 401, 403, 409 |
| `DELETE …/:conversationId/members/:userId` | `removeMember` | Leave or remove | `MemberParamsDto` | `{ message }` | 401, 403 |
| `PATCH …/:conversationId/members/:userId` | `setMemberRole` | Promote, demote | `UpdateMemberRoleDto` | `ConversationResponseDto` | 401, 403, 409 |
| `GET …/:conversationId/messages` | `listMessages` | Thread, cursor-paged (`before`) | `CursorRequestDto` | `MessageResponseDto[]` + `{ nextCursor }` | 401, 403 |
| `POST …/:conversationId/messages` | `sendMessage` | Send text, files, voice, or a video | `SendMessageDto` | `MessageResponseDto` (201) | 400, 401, 403, 404, 409 |
| `PATCH …/messages/:messageId` | `editMessage` | Edit within 15 minutes | `EditMessageDto` | `MessageResponseDto` | 401, 403, 404, 409 |
| `DELETE …/messages/:messageId?scope=me|everyone` | `deleteMessage` | Delete for me, or unsend | `DeleteMessageQueryDto` | `{ message }` | 401, 403, 404 |
| `PUT` / `DELETE …/messages/:messageId/reaction` | `setReaction` | React, remove reaction | `ReactionDto` | `MessageResponseDto` | 400, 401, 403, 409 |

## AdminRequestController (`controllers/users/admin-request`, base `/admin-requests`, all signed in)

| Method and path | Service function | Why | Role | Request DTO | Response DTO | Exceptions |
| --- | --- | --- | --- | --- | --- | --- |
| `POST /admin-requests` | `createAdminRequest` | Ask for admin access | user | `CreateAdminRequestDto` | `AdminRequestResponseDto` (201) | 400, 401, 409 |
| `GET /admin-requests` | `listAdminRequests` | Own requests, or all for admins | Signed in | `AdminRequestQueryDto` | `AdminRequestResponseDto[]` + meta | 400, 401 |
| `GET /admin-requests/:requestId` | `getAdminRequest` | One request | Requester or admin | `AdminRequestParamsDto` | `AdminRequestResponseDto` | 401, 403, 404 |
| `PATCH /admin-requests/:requestId/decision` | `decideAdminRequest` | Accept (promotes, same transaction) or reject | admin | `DecideAdminRequestDto` | `AdminRequestResponseDto` | 400, 401, 403, 404, 409 |
| `DELETE /admin-requests/:requestId` | `cancelAdminRequest` | Withdraw a pending request | Requester | `AdminRequestParamsDto` | `AdminRequestResponseDto` | 401, 403, 404, 409 |

## Health

`GET /health` → `{ status: "ok" }`.

## Socket controller

`controllers/chat/realtime/chat-socket.controller.ts` handles each authenticated socket: see [realtime](../realtime/README.md).
