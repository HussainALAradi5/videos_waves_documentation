# Comment

| Field | Type | Rules |
| --- | --- | --- |
| `video` | ObjectId → Video | Required |
| `author` | ObjectId → User | Required; indexed |
| `parent` | ObjectId → Comment \| null | One level of replies: a reply cannot have replies |
| `content` | string | 1–1000 characters, trimmed |
| `visibility` | `CommentVisibility` | `public`, or `restricted` when the video owner restricted the author (visible to the author and the owner until approved) |
| `repliesCount` | number | Public replies only |
| `editedAt` | Date \| null | Drives the "Edited" label |

Indexes: `{ video, parent, deletedAt, createdAt: -1 }` for the top-level list and `{ parent, deletedAt, createdAt }` for replies. Deletion is a soft delete that the deleter can undo for 15 seconds.

The response adds `isVideoOwner` (the author owns the video, which shows the **Owner** badge), `isEdited`, and `isPendingApproval`.
