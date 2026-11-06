# Comment services

`services/videos/comment/comment.service.ts` and `comment-moderation.service.ts`.

### `listComments(videoId, query, viewerId?)` and `listReplies(params, query, viewerId?)`
Top-level comments newest first, or one comment's replies oldest first. Authors hidden by a block are left out. A `restricted` comment is shown only to its author and the video owner. Each row has `isVideoOwner` (the **Owner** badge), `isEdited`, `isPendingApproval`, and `repliesCount`. 404 when the video is missing.

### `createComment(videoId: string, dto: CreateCommentDto, actor: AuthUser): Promise<CommentResponseDto>`
**Business logic:** `dto.parentId` makes it a reply; replies to replies are refused, so there is one level. If the video owner restricted the actor, the comment is `restricted`. Counters change only for public comments (`applyCommentCounters`, same transaction).
**Exceptions:** 404 (video or parent).

### `updateComment(params, dto, actor)`
**Only the author** may edit; admins and the video owner may not rewrite someone's words. Sets `editedAt`. Found by video **and** comment id, so a comment cannot be reached through another video's URL. 403, 404.

### `deleteComment(params: CommentParamsDto, actor: AuthUser): Promise<void>`
**Who:** the author, the **video owner** (moderating their own post), or an admin.
**Business logic:** A top-level comment takes its replies with it (`softDeleteThread`, sharing one `deletedAt`); counters drop by the public total.
**Exceptions:** 403 `AUTH.FORBIDDEN`, 404.

### `restoreComment(params, actor)`
**Undo:** only the person who deleted it, within **15 seconds** (`COMMENT_UNDO_WINDOW_MS`). Restores the thread and its counters.

| Exception | When | HTTP |
| --- | --- | --- |
| `notFound` `COMMENTS.NOT_FOUND` | Not deleted | 404 |
| `forbidden` | Someone else deleted it | 403 |
| `conflict` `COMMENTS.UNDO_EXPIRED` | After the window | 409 |

### `approveComment(params, actor)`
The video owner makes a restricted comment public; counters then include it. 403 when not the owner; 409 `COMMENTS.NOT_PENDING`.
