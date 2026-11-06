# Video service

### `findVideoOrFail(videoId: string): Promise<VideoDocument>`

**Purpose:** Loads an active video or throws. Shared by the video, like, and comment services.

**Exceptions:**
| Exception | When | HTTP status |
| --- | --- | --- |
| `notFound` | Missing or soft-deleted | 404 |

### `listVideos(query: VideoQueryDto, viewerId?: string): Promise<PageResult<VideoResponseDto>>`

**Purpose:** Paged feed with an optional `ownerId` filter and a title `search` (regex-escaped, case-insensitive).

**Business logic:** Paginates in the database and populates `owner` with `userName image` only. When a viewer is signed in, it loads their likes for the page in **one** `$in` query and sets each video's `isLiked`.

**Exceptions:** None.

### `getVideo(videoId: string, viewerId?: string): Promise<VideoResponseDto>`

**Purpose:** One video with its owner and the viewer's `isLiked`. The video and like lookups run in parallel.

**Exceptions:** `notFound` (404).

### `createVideo(dto: CreateVideoDto, file: UploadedVideoFileDto, actor: AuthUser): Promise<VideoResponseDto>`

**Purpose:** Saves a video record for a file the upload middleware already stored and validated.

**Business logic:** Sets `owner = actor.id` (never taken from the client), plus `fileName`, `mimeType`, and `sizeBytes` from the server-side file. If anything later fails, the error handler deletes the stored file.

**Exceptions:** None beyond the middleware errors (400, 413, 415).

### `updateVideo(videoId: string, dto: UpdateVideoDto, actor: AuthUser): Promise<VideoResponseDto>`

**Purpose:** Edits the title and/or description. The DTO is strict, so counters, owner, or file fields are rejected with 400.

**Exceptions:**
| Exception | When | HTTP status |
| --- | --- | --- |
| `notFound` | Missing | 404 |
| `forbidden` | Neither the owner nor an admin | 403 |

### `deleteVideo(videoId: string, actor: AuthUser): Promise<void>`

**Purpose:** Soft-deletes the video, soft-deletes its comments, and removes its likes. The owner or an admin may delete.

**Exceptions:** `notFound` (404), `forbidden` (403).

### `recordView(videoId: string): Promise<void>`

**Purpose:** Atomically increments `viewsCount`. Clients call it on the first play.

**Exceptions:** `notFound` (404).

### `isStreamable(fileName: string): Promise<boolean>`

**Purpose:** The media route streams a file only while its video is active.

**Exceptions:** None.
