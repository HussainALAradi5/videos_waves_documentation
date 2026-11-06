# Session

One signed-in device.

| Field | Type | Rules |
| --- | --- | --- |
| `user` | ObjectId → User | Required |
| `tokenId` | string | Unique; the `jti` of the current refresh token |
| `previousTokenId` | string \| null | The token rotated away last, honoured once inside the grace window |
| `rotatedAt` | Date \| null | When `tokenId` last changed |
| `userAgent` | string | ≤ 200 characters; shown in the sessions list |
| `lastUsedAt` | Date | Updated on refresh |
| `expiresAt` | Date | TTL index removes the row a day after expiry |
| `revokedAt` | Date \| null | Set by sign-out, sign-out everywhere, reuse detection, or a password change |

Indexes: `{ user, revokedAt, lastUsedAt: -1 }` for the sessions list and `{ expiresAt }` with `expireAfterSeconds: 86400`.
