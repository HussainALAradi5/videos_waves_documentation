# Profile and visibility services

## Visibility (`services/users/visibility/visibility.service.ts`)

| Function | Purpose |
| --- | --- |
| `getHiddenUserIds(viewerId?): Promise<string[]>` | Everyone the viewer blocked plus everyone who blocked them. **Every list** (feed, comments, replies, followers, following, search, chat) filters with these ids, so a block hides both people everywhere |
| `isBlockedEitherWay(firstId, secondId): Promise<boolean>` | Used before starting or sending in a chat |
| `findVisibleUserOrFail(userName, viewerId?): Promise<User>` | Someone who blocked the viewer looks missing (404, as on Instagram). Someone the viewer blocked stays reachable, so they can be unblocked |

## Profile (`services/users/profile/profile.service.ts`)

### `getProfile(userName: string, viewerId?: string): Promise<ProfileResponseDto>`
**Purpose:** `GET /users/:userName`: the public user, `bio`, counters, and the viewer's relations `isMe`, `isFollowing`, `followsYou`, `isBlocked`, `isRestricted` (all false for guests and for oneself).
**Exceptions:** `notFound` `USERS.NOT_FOUND` (404), including when the user blocked the viewer.
