# Comment

| Field | Type | Rules |
| --- | --- | --- |
| `video` | ObjectId → Video | Required |
| `author` | ObjectId → User | Required; indexed |
| `content` | string | 1–1000 characters, trimmed |

Index `{ video, deletedAt, createdAt: -1 }` serves the paged comment list. Deletion is a soft delete.
