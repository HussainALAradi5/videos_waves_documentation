# User

| Field | Type | Rules |
| --- | --- | --- |
| `userName` | string | Required, unique, trimmed; 3–30 of `[a-zA-Z0-9_.]` (validated in DTOs) |
| `email` | string | Required, unique, lowercase, ≤ 254 characters |
| `passwordHash` | string | bcrypt hash; `select: false` |
| `role` | `Role` | `admin` \| `user` (default `user`); indexed |
| `image` | string \| null | Avatar URL, ≤ 2048 characters |
| `tokenVersion` | number | Default 0; `select: false`. Incremented to revoke tokens |

Plus the [base entity](../../base/README.md) fields. Deactivation is a soft delete. Uniqueness checks include soft-deleted users, so their email and username are never reused.
