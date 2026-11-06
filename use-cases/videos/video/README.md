# Videos and likes

### UC-VID-01 Anyone browses the feed
- **Actor:** Visitor or user
- **Main flow:** Opens the feed → `GET /videos?page=1` → scrolling near the end loads the next page → typing in search (debounced 400 ms) reloads from page 1 → the sort can be changed (web).
- **Alternate flows:** No videos → empty state. Network error → error state with retry.
- **Result:** Signed-in viewers see `isLiked` for every card.

### UC-VID-02 User uploads a video
- **Actor:** User
- **Main flow:** Upload → picks or drops a video file → title (required) and description → `POST /videos` (multipart) with a progress bar → redirected to the new video.
- **Alternate flows:** Not a video → 415. Over the size limit → 413. No file → "Choose a video file to upload." Guest → redirected to sign in.
- **Result:** A `VID-…` video owned by the uploader. The file is stored under a random UUID name.

### UC-VID-03 Viewer watches a video
- **Main flow:** Opens a video → `GET /videos/:id` → presses play → `POST /videos/:id/views` once for that visit → the view count increases.
- **Alternate flows:** Deleted or missing video → "This video does not exist or was removed."

### UC-VID-04 User likes or unlikes
- **Main flow:** Presses the heart → the UI updates immediately → `PUT` or `DELETE /videos/:id/like` → the server's count replaces the optimistic one.
- **Alternate flows:** Guest → sent to sign in, then back to the video. API failure → the like is rolled back and an error is shown.
- **Result:** At most one like per user per video. Repeating the call never double-counts.

### UC-VID-05 Owner or admin edits or deletes a video
- **Main flow:** Edit → dialog → `PATCH /videos/:id` (title and description only). Delete → confirmation → `DELETE /videos/:id` → back to the feed.
- **Alternate flows:** Not the owner or an admin → 403 (the buttons are hidden for them).
- **Result:** On delete, the video, its comments, and its likes disappear, and its file is no longer streamed.
