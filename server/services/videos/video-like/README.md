# Video like service

### `likeVideo(videoId: string, actor: AuthUser): Promise<VideoLikeResponseDto>`

**Purpose:** Likes a video. Calling it again changes nothing.

**Business logic:** Upserts `(video, user)`. Only when a new row was inserted does it `$inc likesCount` by 1. Returns `{ videoId, isLiked: true, likesCount }`.

**Cases covered:**
| Case | Result |
| --- | --- |
| First like | Count + 1 |
| Repeated like | Count unchanged |

**Exceptions:** `notFound` (404).

### `unlikeVideo(videoId: string, actor: AuthUser): Promise<VideoLikeResponseDto>`

**Purpose:** Removes the like. Calling it again changes nothing.

**Business logic:** Deletes `(video, user)`. Only when a row was deleted does it decrement the count, and the decrement never goes below zero.

**Exceptions:** `notFound` (404).
