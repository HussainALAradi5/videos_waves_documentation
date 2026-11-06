# Video

| Field | Type | Rules |
| --- | --- | --- |
| `title` | string | 1–120 characters, trimmed |
| `description` | string | ≤ 2000 characters |
| `media` | ObjectId → Media | Required, unique: one file per video |
| `owner` | ObjectId → User | Required |
| `likesCount`, `commentsCount`, `viewsCount` | number | Counters |

The response DTO builds `videoUrl`, `thumbnailUrl` (ImageKit `…/ik-thumbnail.jpg`), and `durationSeconds` from the media row. Deletion soft-deletes the video, its comments, and its media, hard-deletes its likes, and then purges the CDN file.
