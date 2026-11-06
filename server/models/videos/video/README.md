# Video

| Field | Type | Rules |
| --- | --- | --- |
| `title` | string | Required, 1–120 characters |
| `description` | string | ≤ 2000 characters, default `''` |
| `fileName` | string | Unique. A server-generated UUID plus a sanitized extension; never the client's name |
| `mimeType` | string | Must start with `video/` |
| `sizeBytes` | number | ≥ 0 |
| `owner` | ObjectId → User | Required |
| `likesCount`, `commentsCount`, `viewsCount` | number | ≥ 0; atomic counters |

`videoUrl` is not stored. The mapper builds it as `PUBLIC_BASE_URL + /media/videos/<fileName>`.

## Indexes
`{ deletedAt, createdAt: -1 }` (feed), `{ owner, createdAt: -1 }` (my videos), `{ likesCount: -1 }`, `{ viewsCount: -1 }`, `{ title: 1 }`, and unique `fileName`.

## Deletion
Soft delete. Its comments are soft-deleted and its likes hard-deleted. The file stays on disk for audit but is no longer streamed.
