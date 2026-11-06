# Message services

`services/chat/message/*`.

### `listMessages(conversationId: string, query: CursorRequestDto, actor: AuthUser): Promise<CursorPage<MessageResponseDto>>`
Newest first, `pageSize` 30 by default, `before=<messageId>` for older pages; `meta.nextCursor` is null at the start of the chat. People who left read up to when they left. Messages the actor deleted for themselves are skipped. Private attachments carry **signed URLs valid for 1 hour**, created at read time, so only members ever receive a link.

### `sendMessage(conversationId: string, dto: SendMessageDto, actor: AuthUser): Promise<MessageResponseDto>`
**Input:** `body`, `attachmentIds` (1–10 `chat_attachment` media), `voiceNoteId` (alone), `sharedVideoId`, `replyToId`; at least one of the first four.
**Business logic:** `assertCanSend` (a member, not a pending request, and in direct chats not blocked either way) → reply target must be in this conversation → shared video must be visible → in one transaction claim the media and `postMessage` (inbox preview, others' unread counts and sort time, un-hide for people who deleted the chat) → after commit, `broadcastMessages` (`message:new`).

| Exception | When | HTTP |
| --- | --- | --- |
| `forbidden` `CHAT.REQUEST_PENDING` | Replying before accepting a request | 403 |
| `forbidden` `CHAT.CANNOT_MESSAGE_USER` | Blocked either way | 403 |
| `notFound` `CHAT.REPLY_NOT_FOUND` | Reply target elsewhere | 404 |
| 400, 409 | Validation, media rules (`claimMedia`) | 400, 409 |

### `editMessage(params, dto, actor)`
The sender may change a **text** message for **15 minutes**. Sets `editedAt` and emits `message:updated`.
Exceptions: 403 `CHAT.NOT_SENDER`; 404 `CHAT.MESSAGE_NOT_FOUND`; 409 `CHAT.CANNOT_EDIT`, `CHAT.EDIT_WINDOW_PASSED`, `CHAT.ALREADY_UNSENT`.

### `deleteMessage(params, scope: DeleteScope, actor)`
`me` hides it for the caller. `everyone` (sender only, 403 `CHAT.NOT_SENDER`) **unsends** it: content, attachments, and reactions are removed for all members, the files are purged from the CDN, and `message:updated` is emitted.

### `setReaction(params, emoji | null, actor)`
Sets, replaces, or removes the caller's reaction (one per person) and emits `message:reaction` with the new counts. 409 `CHAT.ALREADY_UNSENT`.

### Posting helpers (`message-posting.service.ts`)
`postMessage` and `postSystemMessage` run inside the caller's transaction. `broadcastMessages` and `broadcastConversationChange` emit only **after** the commit, so no one sees a message that was rolled back.
