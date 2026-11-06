# Restriction

| Field | Type | Rules |
| --- | --- | --- |
| `restricter` | ObjectId → User | Required |
| `restricted` | ObjectId → User | Required; indexed |

Indexes: unique `{ restricter, restricted }` (one row per pair and direction) and `{ restricter, createdAt: -1 }` for the paged list. Read by comments (pending visibility) and chat (requests, hidden read receipts and presence).
