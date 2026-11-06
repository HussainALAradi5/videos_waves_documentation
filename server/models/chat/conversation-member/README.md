# ConversationMember

One person's membership and personal state in a conversation.

| Field | Type | Rules |
| --- | --- | --- |
| `conversation`, `user` | ObjectId | Unique pair |
| `role` | `MemberRole` | `admin` or `member` (groups) |
| `status` | `MemberStatus` | `accepted` (inbox) or `request` (message requests) |
| `lastReadAt` | Date \| null | Read position; hidden from others when read receipts are off or the reader restricted them |
| `lastMessageAt` | Date \| null | Sort time of this person's inbox |
| `unreadCount` | number | Messages since `lastReadAt` |
| `mutedUntil` | Date \| null | Mute for up to a week, or indefinitely |
| `leftAt` | Date \| null | Left or removed; the history up to then stays readable |
| `hiddenAt` | Date \| null | "Delete chat" for this person; cleared by a newer message |

Index `{ user, leftAt, hiddenAt, status, lastMessageAt: -1 }` serves every inbox folder.
