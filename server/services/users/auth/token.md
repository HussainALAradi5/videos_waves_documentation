# Token service

### `issueTokens(subject: TokenSubject): AuthTokensDto`

**Purpose:** Signs an access and refresh token pair.

**Business logic:**
1. Builds the payload `{ sub, role, tokenVersion, type }`.
2. Signs the access token with `JWT_ACCESS_SECRET` for `JWT_ACCESS_EXPIRES_IN`, and the refresh token with `JWT_REFRESH_SECRET` for `JWT_REFRESH_EXPIRES_IN`.

**Exceptions:** None.

### `verifyToken(token: string, type: TokenType): TokenPayload | null`

**Purpose:** Verifies a token with the secret for its type.

**Cases covered:**
| Case | Result |
| --- | --- |
| Valid token of the expected type | Payload |
| Expired, tampered, or wrong secret | `null` |
| Refresh token passed as an access token (or the reverse) | `null` (the `type` claim differs) |

**Exceptions:** None (failures return `null`).
