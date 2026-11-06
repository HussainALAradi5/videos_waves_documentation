# User service

`services/users/user/user.service.ts`

| Function | Purpose and logic | Exceptions |
| --- | --- | --- |
| `getProfile(userId)` | The signed-in user's own account (`GET /users/me`) | 404 `USERS.NOT_FOUND` |
| `updateProfile(userId, dto)` | Username, email, and bio; checks `assertIdentityAvailable` first | 404, 409 |
| `updateAvatar(dto, actor)` | Claims an `avatar` media (or with `mediaId: null` returns to the standard picture), releases the previous avatar in the same transaction, then purges its CDN file | 404; 400/409 from `claimMedia` |
| `changePassword(userId, dto, userAgent)` | Checks the current password, saves the new hash, **signs every device out**, and returns a fresh session for this one | 400 `USERS.CURRENT_PASSWORD_INCORRECT`, 404 |
| `deactivateAccount(userId)` | Soft-deletes the user and revokes every session | 404 |
| `searchUsers(query, viewerId?)` | People search (Search tab, new message, add to group): **any part** of a username, email, or phone number, ignoring case (`buildPeopleMatch`). Phones match on their digits when the text has at least 3, so `555 0100` finds `+15550100004`. An empty box finds no one. Results go through the shared `paginate` utility and never include the phone. Hidden and deactivated users never appear | — |
| `listUsers(query)` | Admin directory with email and role | — |

## Account status (`services/users/account-status/account-status.service.ts`)

### `setAccountActive(userName: string, dto: UpdateAccountStatusDto, actor: AuthUser): Promise<UserResponseDto>`
**Purpose:** An admin deactivates or reactivates a regular user.
**Business logic:** Sets or clears `deactivatedAt`. Deactivating also signs out every device (sessions revoked, sockets closed). Nothing is deleted: `getHiddenUserIds` adds deactivated accounts for every viewer, so their videos, comments, replies, follower rows, and search results disappear; their profile and video pages return 404 (admins still see the profile, marked inactive); and nobody can start a chat with them. Reactivating shows everything again exactly as it was. Counters are not changed, so a comment count can include hidden comments while their author is deactivated.

| Exception | When | HTTP |
| --- | --- | --- |
| `badRequest` `USERS.CANNOT_DEACTIVATE_SELF` | The admin targets themselves | 400 |
| `forbidden` `USERS.CANNOT_DEACTIVATE_ADMIN` | The target is an admin | 403 |
| `notFound` `USERS.NOT_FOUND` | Unknown user | 404 |

Signing in to a deactivated account returns 403 `AUTH.ACCOUNT_DEACTIVATED`, checked after the password, so it reveals nothing to someone guessing.

## Export and import (`services/users/user-transfer/user-transfer.service.ts`)

| Function | Purpose and logic |
| --- | --- |
| `exportUsers(query)` | The admin list as CSV with the same search, role, and status filters (`buildUserListFilter`), newest first, at most 5000 rows. Columns: `code, userName, email, role, gender, status, videosCount, followersCount, followingCount, createdAt`. Cells starting with `=`, `+`, `-`, or `@` get a leading `'` so spreadsheets do not run them as formulas. No secrets are exported |
| `importUsers(dto, actor)` | Up to 500 rows of `userName, email, gender, role`, validated like registration (including reserved names). Rows whose email or username already exists (deactivated and deleted accounts included), or that repeat inside the file, are **skipped, never overwritten**. Each new account gets a random temporary password, returned once and stored only as a hash |
