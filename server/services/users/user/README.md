# User service

`services/users/user/user.service.ts`

| Function | Purpose and logic | Exceptions |
| --- | --- | --- |
| `getProfile(userId)` | The signed-in user's own account (`GET /users/me`) | 404 `USERS.NOT_FOUND` |
| `updateProfile(userId, dto)` | Username, email, and bio; checks `assertIdentityAvailable` first | 404, 409 |
| `updateAvatar(dto, actor)` | Claims an `avatar` media (or with `mediaId: null` returns to the standard picture), releases the previous avatar in the same transaction, then purges its CDN file | 404; 400/409 from `claimMedia` |
| `changePassword(userId, dto, userAgent)` | Checks the current password, saves the new hash, **signs every device out**, and returns a fresh session for this one | 400 `USERS.CURRENT_PASSWORD_INCORRECT`, 404 |
| `deactivateAccount(userId)` | Soft-deletes the user and revokes every session | 404 |
| `searchUsers(query, viewerId?)` | Public people search (username contains the text, case-insensitive); users hidden by a block never appear | — |
| `listUsers(query)` | Admin directory with email and role | — |
