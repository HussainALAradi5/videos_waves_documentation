# Chat

### UC-CHAT-01 User starts a direct chat
- **Main flow:** Message on a profile (or the new-message button) → `POST /chat/conversations` → the thread opens. One direct chat exists per pair; it stays hidden for the other person until the first message.
- **Alternate flows:** Blocked either way → 403. Their setting allows only people they follow → 403 "only accepts messages from people they follow".

### UC-CHAT-02 Message lands in requests
- **When:** The recipient does not follow the sender, or restricted them.
- **Main flow:** The chat appears under **Requests** for the recipient, with a banner: Accept or Delete. Until accepted the recipient cannot reply, and the sender does not see Seen.
- **Accept:** `POST …/accept` → moves to the inbox. **Delete:** `DELETE …` → hidden.

### UC-CHAT-03 User sends a text message
- **Main flow:** Types (others see "typing…") → Send → `POST …/messages` → the bubble appears in the bubble colour; others receive `message:new` live, and their inbox and badge update.

### UC-CHAT-04 User sends photos, videos, or files
- **Main flow:** Attach → picks up to 10 files → each uploads in the background with progress (remove or retry per file) → Send (waits until all are uploaded) → one message with all files; photos show in a grid, videos open in a player, other files as tiles.
- **Rules:** Images, videos, and PDFs up to 25 MB each. Files are private and reach members only through 1-hour signed links.

### UC-CHAT-05 User sends a voice note
- **Main flow:** Mic → records with a timer and a wave → Send (or Cancel) → uploads → a voice message with play, pause, and progress. Recording stops and sends automatically at 5 minutes.
- **Alternate flows:** Microphone permission refused → a message explaining it is needed.

### UC-CHAT-06 User replies to a message
- **Main flow:** Long-press → Reply (or swipe right on mobile) → "Replying to …" bar → Send → the bubble shows a quote of the original.

### UC-CHAT-07 User reacts
- **Main flow:** Long-press (mobile) or hover (web) → emoji bar → `PUT …/reaction` → reaction pills under the bubble update for everyone live. The same emoji again removes it; another emoji replaces it.

### UC-CHAT-08 User edits or unsends
- **Edit:** Own text message within 15 minutes → `PATCH …/messages/:id` → "Edited".
- **Unsend:** Own message → `DELETE …?scope=everyone` → "Message unsent" for everyone; files removed from the CDN.
- **Delete for me:** Any message → `DELETE …?scope=me` → gone for you only.

### UC-CHAT-09 Seen and presence
- **Main flow:** Opening a thread marks it read → others get `conversation:read` → "Seen" (direct) or "Seen by n" (group) under your latest message. The partner's avatar shows a green dot and "Active now" while online.
- **Privacy:** Read receipts or activity status off, or a restricted pair → nothing is shown.

### UC-CHAT-10 User creates a group
- **Main flow:** New message → pick two or more people (searchable multi-select) → group name → `POST /chat/groups` → the creator is admin; everyone sees "… created the group". People who do not follow the creator get it as a request.
- **Rules:** At most 32 people.

### UC-CHAT-11 Group admin manages the group
- **Main flow (web group info):** Rename, add people, remove someone, make or remove admin. Each change posts a notice.
- **Rules:** Only admins; the last admin cannot step down; when the last admin leaves, the longest-standing member becomes admin.

### UC-CHAT-12 Member leaves, mutes, or deletes a chat
- **Leave:** `DELETE …/members/<me>` → the history up to now stays readable.
- **Mute (web, in chat info):** `PATCH …` with `isMuted` → no sound for it; the inbox row shows a muted icon.
- **Delete chat:** hidden until someone sends a newer message.

### UC-CHAT-13 Inbox folders and badge
- **Main flow:** Messages → Inbox, Requests, Groups, Unread → rows with avatar, presence, preview, time, unread dot → the tab badge shows unread chats and updates live.

### UC-CHAT-14 Chat options (as in e_clinic)
- **Where:** the ⋯ on an inbox row (web) or a long-press on it (mobile), the ⋯ in the chat header, and chat info (web).
- **Mute:** for 8 hours, for 1 week, or until I unmute → `PATCH /chat/conversations/:id { mute: '8h' | '1w' | 'always' | 'off' }` → a muted bell on the row; no sound or unread badge from it; chat info shows "Muted until …". Unmute appears instead while muted.
- **Leave group** (groups): confirmation → you stop getting messages and cannot write; earlier messages stay readable.
- **Delete chat:** confirmation → gone from your list only; it comes back if someone writes again.
- **Messages:** hover (web) or long-press (mobile) → React, Reply, Copy, Edit (15 minutes), Unsend, Delete for me.
