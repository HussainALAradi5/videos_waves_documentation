# Enums

| Enum | Values | Used by |
| --- | --- | --- |
| `Role` | `admin`, `user` | `User.role`, `authorize()`, ownership checks |
| `Gender` | `male`, `female` | `User.gender`, standard avatars |
| `AdminRequestStatus` | `pending`, `accepted`, `rejected`, `cancelled` | `AdminRequest.status` |
| `TokenType` | `access`, `refresh` | JWT `type` claim |
| `SettingKey`, `ThemePreference`, `MessagePermission`, `BubbleColor` | see [UserSettings](../models/users/settings/README.md) | Settings model and DTO |
| `MediaKind` | `video`, `avatar`, `chat_attachment`, `voice_note` | Upload rules, `claimMedia` |
| `MediaProvider` | `imagekit`, `local`, `external` | `Media.provider` |
| `MediaStatus` | `pending`, `attached` | Media lifecycle |
| `MediaVisibility` | `public`, `private` | Delivery (public URL or signed URL) |
| `FeedScope` | `all`, `following` | `GET /videos?scope=` |
| `CommentVisibility` | `public`, `restricted` | Restricted comments |
| `ConversationType` | `direct`, `group` | `Conversation.type` |
| `InboxFolder` | `inbox`, `requests`, `groups`, `unread` | `GET /chat/conversations?folder=` |
| `MemberRole` | `admin`, `member` | Group admin rules |
| `MemberStatus` | `accepted`, `request` | Inbox or requests |
| `MessageKind` | `text`, `media`, `voice`, `video_share`, `system` | `Message.kind` |
| `SystemAction` | `group_created`, `member_added`, `member_removed`, `member_left`, `group_renamed`, `admin_granted`, `admin_revoked` | System notices |
| `DeleteScope` | `me`, `everyone` | Delete message |
| `ChatEvent` | see [realtime](../realtime/README.md#events) | Socket events |
| `ErrorCode` | see [error codes](../../architecture/README.md#error-codes) | `AppError`, the error handler, both clients |
| `SortDirection` | `asc`, `desc` | Page requests |

Files mirror these names under `src/enums/<area>/<feature>/`. The web client mirrors them in `src/enums/` and the mobile app in `lib/enums/` (with `wire` and `fromWire` on each Dart enum).
