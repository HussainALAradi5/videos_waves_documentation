# Conversation

| Field | Type | Rules |
| --- | --- | --- |
| `type` | `ConversationType` | `direct` or `group` |
| `title` | string \| null | Groups only, 1–60 characters |
| `directKey` | string \| null | Sorted pair of user ids; a unique partial index allows one direct chat per pair |
| `lastMessage` | subdocument \| null | `{ message, sender, kind, preview, createdAt }` for the inbox row |
| `lastMessageAt` | Date \| null | Indexed |
| `membersCount` | number | Active members; a group holds at most 32 |
