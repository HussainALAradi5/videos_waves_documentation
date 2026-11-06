# Follow, block, and restrict services

`services/users/follow`, `services/users/block`, `services/users/restriction`. All take a `userName` and the actor. Lists are paged with `RelationQueryDto` and return `UserSummaryResponseDto` rows (`user`, `isFollowing`, `isMe`).

## Follow

| Function | Purpose and logic | Exceptions |
| --- | --- | --- |
| `followUser(userName, actor)` | Idempotent: inserts the row once, and both counters change in the same transaction only when a row was inserted | 400 `SOCIAL.CANNOT_FOLLOW_SELF`; 409 `SOCIAL.UNBLOCK_FIRST` when the actor blocked them; 404 |
| `unfollowUser(userName, actor)` | Idempotent; `removeFollow` changes counters only when a row was deleted | 404 |
| `removeFollow(followerId, followingId)` | Shared by unfollow and block; call inside a transaction | — |
| `listFollows(side, userName, query, viewerId?)` | Followers or following of a visible user, minus people hidden by a block | 404 |
| `toUserSummaries(users, viewerId?)` | One follow lookup for the whole page | — |

## Block

### `blockUser(userName: string, actor: AuthUser): Promise<RelationStateResponseDto>`
**Business logic:** In one transaction: adds the block once, then removes the follow in **both directions** (with counters). From then on each person is hidden from the other's feed, profile, comments, search, and chat.
**Exceptions:** 400 `SOCIAL.CANNOT_BLOCK_SELF`; 404 `USERS.NOT_FOUND`.

`unblockUser` removes the row (follows are not restored). `listBlockedUsers` pages the actor's blocks (`GET /users/me/blocked`).

## Restrict

### `restrictUser(userName: string, actor: AuthUser)`
**Effects, as on Instagram:**
| Area | What changes for the restricted person |
| --- | --- |
| Comments on your videos | New comments are `restricted`: visible only to them and you until you **Approve** |
| Messages | New direct chats from them land in your requests |
| Chat privacy | They never see your read receipts or online status |

**Exceptions:** 400 `SOCIAL.CANNOT_RESTRICT_SELF`; 404.

`isRestrictedBy(restricterId, userId)` is the check used by comments and chat. `unrestrictUser` and `listRestrictedUsers` mirror the block functions.
