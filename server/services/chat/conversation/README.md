# Conversation and group services

`services/chat/conversation/*`. Every function checks membership first (`findMembershipOrFail`: 403 `CHAT.NOT_A_MEMBER` for outsiders; 404 `CHAT.NOT_FOUND`).

## Access rules (`chat-access.service.ts`)

`resolveInviteStatus(inviterId, inviteeId)`: an invitation lands in the invitee's **inbox** when they follow the inviter and have not restricted them; otherwise in their **requests**. `findAdministeredGroupOrFail` refuses direct chats (400 `CHAT.GROUP_ONLY`) and non-admins (403 `CHAT.ADMIN_REQUIRED`).

## Conversations (`conversation.service.ts`)

| Function | Purpose and logic | Exceptions |
| --- | --- | --- |
| `listConversations(query, actor)` | `folder` = `inbox`, `requests`, `groups`, or `unread`, most recent first, paged. Views are built with a fixed number of queries per page (`buildConversationViews`) | — |
| `getConversation(id, actor)` | One conversation with members; `lastReadAt` and `isOnline` are `null` where privacy hides them | 403, 404 |
| `getUnreadTotal(actor)` | Unread chats for the Messages badge | — |
| `startDirect(dto, actor)` | The one direct chat per pair (`directKey`), created on first use and hidden for the other person until the first message. Lands in their requests unless they follow you and have not restricted you | 400 `CHAT.CANNOT_MESSAGE_SELF`; 403 `CHAT.CANNOT_MESSAGE_USER` (block); 403 `CHAT.MESSAGES_FOLLOWING_ONLY` (their setting); 404 |
| `updateConversation(id, dto, actor)` | Rename (group admins) and mute (any member, for themselves): `mute` is `8h`, `1w`, `always`, or `off` and sets `mutedUntil`; the older `isMuted` boolean still works | 400, 403 |
| `acceptRequest(id, actor)` | Moves a request into the inbox so the person can reply | 409 when not a request |
| `hideConversation(id, actor)` | "Delete chat" or decline a request: hidden until a newer message | 403 |
| `markConversationRead(id, actor)` | Resets the unread count and tells the others (`conversation:read`), except people the reader restricted | 403 |

## Groups (`group.service.ts`)

| Function | Purpose and logic | Exceptions |
| --- | --- | --- |
| `createGroup(dto, actor)` | Title and at least 2 invitees; the creator is admin. Members are created **before** the "created the group" notice, so everyone receives it. Invitees who do not follow the creator get it as a request | 400; 403 (blocked invitee); 404; 409 `CHAT.GROUP_FULL` (over 32) |
| `addMembers(id, dto, actor)` | Admins only | 403; 409 `CHAT.ALREADY_MEMBER`, `CHAT.GROUP_FULL` |
| `removeMember(id, userId, actor)` | Yourself = leave; someone else needs an admin. When the last admin leaves, the longest-standing member becomes admin. The removed person keeps the history up to now | 403 |
| `setMemberRole(id, userId, role, actor)` | Promote or demote | 409 `CHAT.LAST_ADMIN`; 409 `CHAT.REQUEST_PENDING` |

Every change posts a system message and emits `conversation:updated`.
