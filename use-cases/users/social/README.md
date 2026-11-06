# Profiles, follow, block, restrict, settings

### UC-SOC-01 Anyone views a profile
- **Main flow:** Taps a name or avatar → `/u/:userName` → avatar, Videos, Followers, Following, bio, and the video grid. Signed-in viewers see Follow (or Follow back / Following), Message, and ⋯.
- **Alternate flows:** The person blocked the viewer → "This account is not available" (404). The viewer blocked them → Unblock only, no videos.

### UC-SOC-02 User follows or unfollows
- **Main flow:** Follow → the button and follower count change at once → `PUT /users/:userName/follow` → the Following feed now includes their videos.
- **Alternate flows:** Guest → sign in, then back. Failure → rolled back with a message. Following yourself → 400; following someone you blocked → 409.
- **Result:** Idempotent; counters on both users change together.

### UC-SOC-03 User browses followers or following
- **Main flow:** Taps a count → a sheet or dialog with a paged list and inline Follow buttons; people hidden by a block are left out.

### UC-SOC-04 User finds someone by email or phone
- **Main flow:** Search tab (or New message) → types the person's **full** email or phone number with the country code → `GET /users/search` → that one account, with Follow (or ready to message).
- **Alternate flows:** A partial email, a name, or a username → no request is sent and the page explains what to type. No account uses it → "No account uses that email or phone number."
- **Privacy:** phone numbers are optional (set at sign-up or in Settings) and never shown to other people.

### UC-SOC-05 User blocks someone
- **Main flow:** ⋯ → Block → confirmation → `PUT /users/:userName/block` → follows end both ways → each disappears from the other's feed, comments, search, and chat; neither can message the other.
- **Undo:** ⋯ → Unblock, or Settings → Blocked accounts. Follows are not restored.

### UC-SOC-06 User restricts someone
- **Main flow:** ⋯ → Restrict → `PUT /users/:userName/restrict`. From then on: their new comments on your videos are visible only to them and you, with a "Restricted" note and **Approve**; their new direct messages land in your requests; they never see your Seen or online status.
- **Undo:** ⋯ → Unrestrict, or Settings → Restricted accounts.

### UC-SOC-07 User changes their profile picture
- **Main flow:** Settings → Change photo → picks an image → uploads to the CDN with progress → `PATCH /users/me/avatar` → shown everywhere.
- **Alternate flows:** "Use the standard picture" → `mediaId: null` → back to the gender avatar. Wrong type or over 5 MB → refused.

### UC-SOC-08 User changes a setting
- **Main flow:** Settings → a switch or the custom dropdown → the change applies at once (theme switches instantly) → `PATCH /users/me/settings` → rolled back with a message on failure.
- **Effects:** Activity status and read receipts off → others see no online dot or Seen. "Who can message you: People I follow" → others cannot start a chat. Bubble colour → your chat bubbles.

### UC-ADM-01 Admin deactivates or reactivates a user
- **Actor:** Admin. **Where:** the Users page (web `/admin/users`; mobile Settings → Manage users) or the ⋯ menu on a profile.
- **Main flow:** Deactivate → confirmation → `PATCH /users/:userName/status { isActive: false }` → the row shows **Inactive** in red. The person is signed out everywhere and cannot sign in ("deactivated by an admin"). Their profile, videos, and comments are hidden from everyone; admins still see the profile with an Inactive badge.
- **Reactivate:** Activate → everything is visible again as before; nothing was deleted.
- **Rules:** Admins cannot be deactivated (no button; 403), and an admin cannot target themselves (400).

### UC-ADM-02 Admin browses, searches, and filters accounts
- **Main flow:** Users page → every account (active, inactive, admins) with **Admin** / **Member** and **Active** / **Inactive** badges → search by username or email (debounced) → filter by role and status → pages of 20.

### UC-ADM-03 Admin exports accounts
- **Main flow (web):** Export CSV → downloads `videos-waves-users.csv` with the current search and filters (up to 5000 rows, no passwords).

### UC-ADM-04 Admin imports accounts
- **Main flow (web):** Import CSV → optionally download the template → pick a file → "n accounts ready", listing rows that miss a username, email, or gender → Import → "x created, y skipped" with each reason → **Download new accounts** (a CSV of one-time temporary passwords, shown once).
- **Rules:** At most 500 rows; existing emails or usernames are skipped, never changed; usernames follow the registration rules.
