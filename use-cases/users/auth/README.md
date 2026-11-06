# Accounts and sessions

### UC-AUTH-01 Visitor registers
- **Actor:** Visitor
- **Preconditions:** None.
- **Main flow:** Submits a username, email, and password → the client validates the same rules as the server → `POST /auth/register` → the account is created with role `user` → the session is stored → the feed opens, signed in.
- **Alternate flows:** Weak password or bad username → field errors shown before any request. Email or username taken → 409 shown as a toast or snack bar.
- **Result:** A new `USR-…` user; `createdBy` is the user.

### UC-AUTH-02 User signs in
- **Actor:** User
- **Main flow:** Username or email plus password → `POST /auth/login` → session stored → returns to the `redirect` page or the feed.
- **Alternate flows:** Wrong credentials → 401 "Invalid username, email, or password." Deactivated account → the same 401.
- **Result:** Signed in on that device.

### UC-AUTH-03 Session renews silently
- **Actor:** Client (on behalf of a user)
- **Main flow:** The access token expires → a request returns 401 → the client refreshes once (shared by parallel requests) → the original request is retried.
- **Failure flow:** The refresh is rejected (expired, revoked, or user deactivated) → tokens are cleared → "Your session expired" → guest state.

### UC-AUTH-04 User signs out
- **Main flow:** `POST /auth/logout` → `tokenVersion` is incremented → local tokens are cleared → feed. Every other device's refresh token stops working.

### UC-AUTH-05 User changes password
- **Main flow:** Current, new, and confirmation passwords → `PATCH /users/me/password` → a new session is returned for this device; other sessions are revoked.
- **Alternate flows:** Wrong current password → 400. Confirmation mismatch → field error.

### UC-AUTH-06 User deactivates their account
- **Main flow:** Confirms in a dialog → `DELETE /users/me` → soft delete and token revocation → signed out.
- **Result:** The account can no longer sign in. Its email and username stay reserved.
