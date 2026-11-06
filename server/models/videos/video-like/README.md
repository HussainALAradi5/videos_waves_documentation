# VideoLike

| Field | Type | Rules |
| --- | --- | --- |
| `video` | ObjectId → Video | Required |
| `user` | ObjectId → User | Required; indexed |

Unique index `{ video, user }`: one like per user per video. Likes are join rows, so they are hard-deleted. The original design kept a `likedBy` array on each video, which grew without limit and was loaded with every video. This collection replaces it.
