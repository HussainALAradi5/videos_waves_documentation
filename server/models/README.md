# Models

Every model applies the [base entity](base/README.md) plugin (`code`, audit fields, soft delete).

| Model | File | Collection |
| --- | --- | --- |
| [Base entity](base/README.md) | `models/base/base-entity.model.ts` | — (a plugin) |
| [User](users/user/README.md) | `models/users/user/user.model.ts` | `users` |
| [Session](users/session/README.md) | `models/users/session/session.model.ts` | `sessions` |
| [UserSettings](users/settings/README.md) | `models/users/settings/user-settings.model.ts` | `usersettings` |
| [Follow](users/follow/README.md) | `models/users/follow/follow.model.ts` | `follows` |
| [Block](users/block/README.md) | `models/users/block/block.model.ts` | `blocks` |
| [Restriction](users/restriction/README.md) | `models/users/restriction/restriction.model.ts` | `restrictions` |
| [AdminRequest](users/admin-request/README.md) | `models/users/admin-request/admin-request.model.ts` | `adminrequests` |
| [Media](media/media/README.md) | `models/media/media/media.model.ts` | `media` |
| [Video](videos/video/README.md) | `models/videos/video/video.model.ts` | `videos` |
| [VideoLike](videos/video-like/README.md) | `models/videos/video-like/video-like.model.ts` | `videolikes` |
| [Comment](videos/comment/README.md) | `models/videos/comment/comment.model.ts` | `comments` |
| [Conversation](chat/conversation/README.md) | `models/chat/conversation/conversation.model.ts` | `conversations` |
| [ConversationMember](chat/conversation-member/README.md) | `models/chat/conversation-member/conversation-member.model.ts` | `conversationmembers` |
| [Message](chat/message/README.md) | `models/chat/message/message.model.ts` | `messages` |
| [MessageReaction](chat/message-reaction/README.md) | `models/chat/message-reaction/message-reaction.model.ts` | `messagereactions` |
| [Migration](system/migration/README.md) | `models/system/migration/migration.model.ts` | `migrations` |

## Relationships

| Model | Relation | Target | Foreign key | On delete |
| --- | --- | --- | --- | --- |
| Video | many-to-one | User (owner) | `owner` | User soft delete keeps the video |
| Video | one-to-one | Media | `media` (unique) | Media released and its CDN file purged with the video |
| VideoLike | many-to-one | Video, User | `video`, `user` | Hard-deleted when the video is deleted |
| Comment | many-to-one | Video, User (author) | `video`, `author` | Soft-deleted with the video |
| Comment | many-to-one (replies) | Comment | `parent` | A top-level comment takes its replies with it |
| User | many-to-one | Media (avatar) | `avatar` | The old avatar file is purged when replaced |
| Follow | many-to-many | User ↔ User | `follower`, `following` | Removed both ways when either blocks the other |
| Block, Restriction | many-to-many | User ↔ User | `blocker`/`blocked`, `restricter`/`restricted` | — |
| Session, UserSettings | many-to-one / one-to-one | User | `user` | Sessions revoked on password change or deactivation |
| ConversationMember | many-to-one | Conversation, User | `conversation`, `user` | `leftAt` keeps history readable |
| Message | many-to-one | Conversation, User (sender) | `conversation`, `sender` | Unsend clears content and purges attachments |
| Message | many-to-many | Media | `attachments[]` | Released on unsend |
| MessageReaction | many-to-one | Message, User | `message`, `user` | Removed on unsend |

## Counters

These are denormalized and change only with atomic `$inc`, inside the transaction that changes the source rows. A decrement is guarded with `{ counter: { $gte: n } }`, so it never goes below zero.

| Counter | Changed by |
| --- | --- |
| `Video.likesCount` | Like (only when a row was inserted) and unlike (only when one was deleted) |
| `Video.commentsCount`, `Comment.repliesCount` | Public comments only (`applyCommentCounters`); a restricted comment counts when it is approved |
| `Video.viewsCount` | `POST /videos/:id/views` |
| `User.followersCount`, `followingCount` | Follow, unfollow, and block (which removes follows both ways) |
| `User.videosCount` | Create and delete video |
| `ConversationMember.unreadCount` | Incremented for other members on each message; reset by `markConversationRead` |
