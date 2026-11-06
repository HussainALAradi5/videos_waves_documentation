# Follow

| Field | Type | Rules |
| --- | --- | --- |
| `follower` | ObjectId → User | Required |
| `following` | ObjectId → User | Required; indexed |

Indexes: unique `{ follower, following }` (one row per pair and direction) and `{ follower, createdAt: -1 }` for the paged list. `following` is also indexed for the followers list. Follow and unfollow are idempotent.
