# Comments

### UC-CMT-01 User comments
- **Actor:** User
- **Main flow:** Writes a comment (1–1000 characters) → `POST /videos/:id/comments` → it appears at the top and the video's comment count increases.
- **Alternate flows:** Guest → "Sign in to join the conversation." Empty text → the post button is disabled.

### UC-CMT-02 Author edits a comment
- **Main flow (web):** Edit icon → inline editor → Save → `PATCH /videos/:id/comments/:commentId`.
- **Alternate flows:** Not the author (including admins) → 403; the edit icon is shown only to the author.

### UC-CMT-03 Author or admin deletes a comment
- **Main flow:** Delete icon → confirmation → `DELETE …/comments/:commentId` → removed; the count decreases.
- **Alternate flows:** Other users → 403 (icon hidden). Comment addressed through a different video's URL → 404.
