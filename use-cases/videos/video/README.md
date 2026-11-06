# Videos, uploads, and likes

### UC-VID-01 Anyone browses the feed
- **Main flow:** Home → `GET /videos` → cards fade in; scrolling loads the next page; search (debounced) reloads from page 1. Signed-in users switch between **For you** and **Following**.
- **Alternate flows:** Nobody followed yet → "Follow people to see their waves here." Network error → retry.

### UC-VID-02 User uploads several videos
- **Main flow:** Upload → picks or drops up to 10 videos → each uploads **straight to the CDN** with its own progress bar, titled from its file name → edits titles and descriptions → **Publish** (spinner, no double submit) → `POST /videos` for each, in order → the feed refreshes.
- **Alternate flows:** More than 10 → the extra files are skipped with a message. A failed upload → Retry on that tile. Wrong type or over 100 MB → refused at registration. Remove a tile → the pending upload is discarded.

### UC-VID-03 Viewer watches a video
- **Main flow:** Opens a video (the thumbnail animates into the player) → play → `POST /videos/:id/views` once per visit.
- **Alternate flows:** Deleted, missing, or hidden by a block → "This video does not exist or was removed."

### UC-VID-04 User likes or unlikes
- **Main flow:** Heart (or double-tap on the web, with a heart burst) → the UI updates at once → `PUT` or `DELETE …/like` → the server count replaces the optimistic one.
- **Alternate flows:** Guest → sign in, then back. Failure → rolled back.

### UC-VID-05 Owner edits or deletes a video
- **Main flow:** ⋯ → Edit → sheet or dialog → Save → `PATCH /videos/:id`. ⋯ → Delete → confirmation → `DELETE /videos/:id` → back to the feed.
- **Who:** Edit: the owner. Delete: the owner or an admin. Others do not see the menu (403 if called).
- **Result:** The video, its comments, likes, and CDN file are removed.

### UC-VID-06 User shares a video in chat
- **Main flow (web):** Share → Copy link, or Send in a message → Messages opens with the video attached to the composer → open a conversation → Send → the message shows a video card that opens the video.
