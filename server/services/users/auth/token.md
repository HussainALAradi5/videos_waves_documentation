# Token service

`services/users/auth/token.service.ts`

### `issueTokens(subject: TokenSubject, refreshTokenId: string): AuthTokensDto`

**Purpose:** Signs an access and refresh token pair for one session.

**Business logic:**
1. The payload is `{ sub, role, tokenVersion, sid, type }`, where `sid` is the session id.
2. The access token is signed with `JWT_ACCESS_SECRET` for `JWT_ACCESS_EXPIRES_IN`.
3. The refresh token is signed with `JWT_REFRESH_SECRET` for `JWT_REFRESH_EXPIRES_IN` and carries `jti = refreshTokenId`, which must match the session's current `tokenId` (see `refreshSession`).

**Exceptions:** None.

### `refreshExpiryFromNow(): Date`

**Purpose:** When a refresh token issued now would expire, read from a signed token so `Session.expiresAt` always matches the config.

### `verifyToken(token: string, type: TokenType): TokenPayload | null`

**Purpose:** Verifies a token with the secret for its type.

**Cases covered:**
| Case | Result |
| --- | --- |
| Valid token of the expected type with a session id | Payload |
| Expired, tampered, or signed with another secret | `null` |
| Refresh token passed as an access token (or the reverse) | `null` (the `type` claim differs) |
| Token from before sessions existed (no `sid`) | `null`, so the user signs in again |

**Exceptions:** None (failures return `null`). Callers decide the HTTP error.
