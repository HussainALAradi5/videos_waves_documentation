# Accounts and sessions

### UC-AUTH-01 Visitor registers
- **Actor:** Visitor
- **Main flow:** Username, email, password, and gender → the client checks the server's rules → `POST /auth/register` → role `user`, a session for this device → the feed opens, signed in. The profile picture is the standard one for the chosen gender.
- **Alternate flows:** Weak password, bad username, or no gender → field errors before any request. Email or username taken → 409.
- **Result:** A new `USR-…` user; `createdBy` is the user.

### UC-AUTH-02 User signs in
- **Main flow:** Username or email plus password → `POST /auth/login` → a new device session → returns to the `redirect` page or the feed.
- **Alternate flows:** Wrong credentials or deactivated account → one 401 message.

### UC-AUTH-03 Session renews silently
- **Main flow:** The access token expires → a request returns 401 → the client refreshes once (across tabs on the web) → the request is retried.
- **Alternate flows:** Two tabs refresh at once → both keep working (grace window). Offline → the app stays signed in and retries later. The refresh returns 401 (expired, revoked, or reused) → "Your session expired" → guest.

### UC-AUTH-04 User signs out
- **Main flow:** Sign out → `POST /auth/logout` → only **this device** is signed out; other devices stay signed in.
- **Variant:** Settings → Sessions → "Sign out everywhere" (`POST /auth/logout-all`) or sign out one listed device (`DELETE /auth/sessions/:id`); its sockets close at once.

### UC-AUTH-05 User changes password
- **Main flow:** Current, new, and confirmation passwords → `PATCH /users/me/password` → every other device is signed out; this one gets a fresh session.
- **Alternate flows:** Wrong current password → 400.

### UC-AUTH-06 User deactivates their account
- **Main flow:** Confirms → `DELETE /users/me` → soft delete and every session revoked → signed out. The email and username stay reserved.
