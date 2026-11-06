# User

| Field | Type | Rules |
| --- | --- | --- |
| `userName` | string | Required, unique, trimmed; 3–30 of `[a-zA-Z0-9_.]` (validated in DTOs) |
| `email` | string | Required, unique, lowercase, ≤ 254 characters |
| `phone` | string \| null | Optional and private, E.164 (`+97333001122`); unique among accounts that have one; only for exact-match people search; shown to the owner and admins only |
| `passwordHash` | string | bcrypt hash; `select: false` |
| `role` | `Role` | `admin` \| `user` (default `user`); indexed |
| `gender` | `Gender` \| null | `male` \| `female`; required at registration; picks the standard avatar |
| `avatar` | ObjectId → Media \| null | The uploaded profile picture |
| `avatarUrl` | string \| null | Delivery URL of `avatar`, ≤ 2048 characters; null means the standard picture |
| `bio` | string | ≤ 150 characters |
| `followersCount`, `followingCount`, `videosCount` | number | Counters (see [models](../../README.md#counters)) |
| `deactivatedAt`, `deactivatedBy` | Date \| null, ObjectId \| null | Set by an admin; the account cannot sign in and is hidden with its content until reactivated (indexed) |
| `tokenVersion` | number | `select: false`; incremented to revoke every session at once |

Plus the [base entity](../../base/README.md) fields. **Two different states:** a user deactivating their own account (`DELETE /users/me`) is a soft delete; an admin deactivating someone (`deactivatedAt`) is a reversible status that deletes nothing. Uniqueness checks include soft-deleted users, so an email or username is never reused.

**Standard avatars:** when `avatarUrl` is null, clients show `male.svg`, `female.svg`, or `neutral.svg` (unknown gender, such as migrated accounts). The files live in the web client's `public/avatars/` and the mobile app's `assets/avatars/`.
