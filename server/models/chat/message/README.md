# Message

| Field | Type | Rules |
| --- | --- | --- |
| `conversation`, `sender` | ObjectId | Required |
| `kind` | `MessageKind` | `text`, `media`, `voice`, `video_share`, `system` |
| `body` | string \| null | ≤ 2000 characters |
| `attachments` | ObjectId[] → Media | Up to 10 chat files, or one voice note |
| `replyTo` | ObjectId → Message \| null | Must be in the same conversation |
| `sharedVideo` | ObjectId → Video \| null | A video shared into the chat |
| `systemAction`, `systemTarget` | | Group events (created, member added or removed, left, renamed, admin granted or revoked) |
| `editedAt` | Date \| null | Edits are allowed for 15 minutes |
| `unsentAt` | Date \| null | Unsent for everyone: content, attachments, and reactions removed |
| `deletedFor` | ObjectId[] | "Delete for me" |

Index `{ conversation, _id: -1 }` serves the cursor-paged thread.
