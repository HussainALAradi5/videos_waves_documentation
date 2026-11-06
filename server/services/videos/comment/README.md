# Comment service

### `listComments(videoId: string, query: CommentQueryDto): Promise<PageResult<CommentResponseDto>>`

**Purpose:** Paged comments of an active video, newest first, with each author's `userName` and `image`.

**Exceptions:** `notFound` (404) when the video is missing.

### `createComment(videoId: string, dto: CommentBodyDto, actor: AuthUser): Promise<CommentResponseDto>`

**Purpose:** Adds a comment (trimmed, 1–1000 characters) and increments `commentsCount`.

**Exceptions:** `notFound` (404) when the video is missing.

### `updateComment(params: CommentParamsDto, dto: CommentBodyDto, actor: AuthUser): Promise<CommentResponseDto>`

**Purpose:** Edits a comment. **Only the author** may edit; admins may not rewrite another user's words.

**Business logic:** Finds the comment by **both** `videoId` and `commentId`, so a comment cannot be reached through another video's URL.

**Exceptions:**
| Exception | When | HTTP status |
| --- | --- | --- |
| `notFound` | Missing, deleted, or not on that video | 404 |
| `forbidden` | Not the author | 403 |

### `deleteComment(params: CommentParamsDto, actor: AuthUser): Promise<void>`

**Purpose:** Soft-deletes a comment. The author or an admin (moderation) may delete. Decrements `commentsCount` only when a row changed.

**Exceptions:** `notFound` (404), `forbidden` (403).
