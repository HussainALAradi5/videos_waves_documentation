# Comments and replies

### UC-CMT-01 User comments or replies
- **Main flow:** Writes a comment (1–1000 characters) → `POST …/comments` → it appears at the top. Reply → a composer under the comment → `POST` with `parentId` → "View replies (n)".
- **Alternate flows:** Guest → "Sign in to like and comment." Replies to replies are not allowed (one level). The video owner restricted the author → the comment is visible only to the author and the owner, marked "Restricted", until approved.

### UC-CMT-02 Video owner's comments carry a badge
- **Result:** Every comment or reply by the person who posted the video shows the gradient **Owner** badge next to their name.

### UC-CMT-03 Author edits a comment
- **Main flow:** ⋯ → Edit → inline editor (grows in) → Save (spinner) or Cancel/Esc → `PATCH …/comments/:id` → "Edited" label.
- **Rule:** Only the author, not admins or the video owner.

### UC-CMT-04 Comment is deleted, with Undo
- **Who:** The author, the video owner, or an admin.
- **Main flow:** ⋯ → Delete → the comment disappears at once → "Comment deleted · Undo" → `DELETE …/comments/:id`. Undo within 15 seconds → `PATCH …/restore` → back in the same place with its replies and counts.
- **Alternate flows:** Undo after the window → 409 "can no longer be undone". The request fails → the comment comes back with a message.

### UC-CMT-05 Video owner approves a restricted comment
- **Main flow:** Approve on a "Restricted" comment → `PATCH …/approve` → visible to everyone; counts include it.
