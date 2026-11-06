# Notification service

`services/notifications/notification/notification.service.ts`. Repository: `notificationRepository` (base entity, soft delete). Model fields: `user`, `kind` (`NotificationKind`), `actor` (null for system), `targetId`, `subject` (at most 120 characters), `readAt`.

| Kind | Sent by | To | Link built by the mapper |
| --- | --- | --- | --- |
| `follow` | `followUser` (new follow only) | The followed user | `/u/<actor>` |
| `follow_request` | `followUser` on a private account (new request only) | The private account | `/u/<actor>` |
| `follow_accepted` | `acceptFollowRequest` | The requester | `/u/<actor>` |
| `like` | `likeVideo` (new like only) | Video owner | `/videos/<id>` |
| `comment` | `createComment` (not while pending for a restricted author) | Video owner, unless it is a reply to the owner's own comment | `/videos/<id>` |
| `reply` | `createComment` with a parent | Parent comment's author | `/videos/<id>` |
| `group_added` | `createGroup`, `addMembers` | Each invitee | `/messages/<id>` |
| `admin_accepted` / `admin_rejected` | `decideAdminRequest` | The requester (`actor` null) | `/settings` |

Links are in-app paths that both clients route the same way.

### `notify(input: NotifyInput): Promise<void>`
**Business logic:** Never for your own action (`actorId === userId`). Stores the row, maps it (`toNotificationResponse` renders `message` and `link`), and emits `notification:new` to `user:<id>`.
**Called:** after the action's transaction commits, so a rolled-back action never notifies.
**Exceptions:** none; failures are logged (best effort).

### `listNotifications(query: NotificationQueryDto, actor: AuthUser): Promise<PageResult<NotificationResponseDto>>`
**Business logic:** Newest first; `filter=unread` keeps `readAt: null`. Actors from `getHiddenUserIds` (blocks both ways, deactivated) are excluded.

### `getUnreadCount(actor: AuthUser): Promise<{ unreadCount: number }>`
Same exclusions as the list.

### `markNotificationRead(notificationId: string, actor: AuthUser): Promise<NotificationResponseDto>`
**Exceptions:** 404 `NOTIFICATIONS.NOT_FOUND` when it is not the actor's.

### `markAllNotificationsRead(actor: AuthUser): Promise<{ updated: number }>`
