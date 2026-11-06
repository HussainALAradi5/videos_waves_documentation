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

## Private accounts and follow requests

`services/users/privacy/privacy.service.ts` and `services/users/follow-request/follow-request.service.ts`. A private account is the `privateAccount` setting. Requests live in `FollowRequest` (`requester`, `target`, unique pair, hard-deleted like the other relations).

| Function | Purpose and logic | Exceptions |
| --- | --- | --- |
| `canViewContentOf(ownerId, viewerId?)` | The owner always; anyone for a public account; only approved followers for a private one | — |
| `findLockedOwnerIds(viewerId?)` | Private accounts whose videos the viewer may not see (feeds exclude them) | — |
| `followUser` on a private account | Stores a request once (notifies `follow_request`) and returns `isRequested: true`; no counters change | as follow |
| `unfollowUser` | Also cancels a pending request | 404 |
| `listFollowRequests(query, actor)` | People waiting, newest first, minus hidden users | — |
| `acceptFollowRequest(userName, actor)` | Transaction: removes the request and adds the follow with both counters; notifies `follow_accepted` | 404 `SOCIAL.REQUEST_NOT_FOUND` |
| `declineFollowRequest(userName, actor)` | Removes the request; the requester is not told | 404 when the user is hidden |
| `acceptAllFollowRequests(userId)` | Called when `privateAccount` becomes `false` | — |

Blocking also deletes pending requests in both directions. `findVideoOrFail` answers 403 `SOCIAL.PRIVATE_ACCOUNT` when a viewer (or a guest, for the video page and comments) may not see the owner's content.

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
