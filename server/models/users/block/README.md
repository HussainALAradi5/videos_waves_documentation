# Block

| Field | Type | Rules |
| --- | --- | --- |
| `blocker` | ObjectId → User | Required |
| `blocked` | ObjectId → User | Required; indexed |

Indexes: unique `{ blocker, blocked }` (one row per pair and direction) and `{ blocker, createdAt: -1 }` for the paged list. A block hides both people from each other everywhere (see `getHiddenUserIds`).
