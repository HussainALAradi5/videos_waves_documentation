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

### UC-SOC-04 User searches for people
- **Main flow:** Search tab → types (debounced) → `GET /users/search` → results with Follow.

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
