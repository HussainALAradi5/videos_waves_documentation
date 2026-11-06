# Auth and session services

`services/users/auth/auth.service.ts` and `services/users/auth/session.service.ts`. Tokens are described in [token](token.md).

## Auth service

### `buildAuthResponse(user: User, userAgent: string): Promise<AuthResponseDto>`
**Purpose:** Starts a session on this device (`startSession`) and returns `{ accessToken, refreshToken, user }`. Shared by register, login, and change password.

### `assertIdentityAvailable(identity: { email?, userName? }, exceptId?: string): Promise<void>`
**Purpose:** Rejects an email or username used by another account, including deactivated ones.

| Exception | When | HTTP |
| --- | --- | --- |
| `conflict` `USERS.EMAIL_TAKEN` / `USERS.USER_NAME_TAKEN` | Identity in use | 409 |

### `register(dto: RegisterDto, userAgent: string): Promise<AuthResponseDto>`
**Business logic:** `assertIdentityAvailable` → pre-generates the user id so the user is their own `createdBy` → hashes the password (bcrypt, `BCRYPT_SALT_ROUNDS`) → saves with role `user` and the required `gender` → `buildAuthResponse`.
**Exceptions:** 409 as above; 400 from the DTO (weak password, invalid username, missing gender).

### `login(dto: LoginDto, userAgent: string): Promise<AuthResponseDto>`
**Business logic:** A value containing `@` is an email (lowercased), otherwise a username. Unknown accounts still run one bcrypt comparison against `TIMING_SAFE_DUMMY_HASH`, so the response time does not reveal which accounts exist.

| Exception | When | HTTP |
| --- | --- | --- |
| `unauthorized` `AUTH.INVALID_CREDENTIALS` | Unknown, deactivated, or wrong password (one message for all) | 401 |

## Session service

### `resolveAccessToken(token: string): Promise<AuthUser>`
**Purpose:** Used by the HTTP `authenticate` middleware and the socket handshake. Verifies the access token and reloads the role, `tokenVersion`, and the session, so deactivation, revoked devices, and role changes take effect at once.
**Exceptions:** `unauthorized` `AUTH.TOKEN_INVALID` (401) for a bad, expired, or revoked token.

### `startSession(user: User, userAgent: string): Promise<AuthTokensDto>`
**State change:** Creates a `Session` with a new `tokenId` and returns its first token pair.

### `refreshSession(refreshToken: string): Promise<AuthTokensDto>`
**Business logic:**
| Case | Result |
| --- | --- |
| Token is the session's current `tokenId` | Rotates `tokenId` atomically and returns new tokens |
| Token is `previousTokenId`, within 30 seconds of the rotation | Returns tokens for the current id (two tabs refreshing at once) |
| Another request rotated the same token a moment ago | Shares that result instead of forking the session |
| Any other older token | **Reuse detected:** the session is revoked and its sockets closed |
| Invalid token, revoked session, or changed `tokenVersion` | Rejected |

| Exception | When | HTTP |
| --- | --- | --- |
| `unauthorized` `AUTH.REFRESH_INVALID` | Invalid, expired, revoked | 401 |
| `unauthorized` `AUTH.REFRESH_REUSED` | Reuse of a rotated token | 401 |

### `listSessions(actor: AuthUser): Promise<SessionResponseDto[]>`
**Purpose:** The user's active devices, newest first, with `isCurrent` for this one.

### `revokeSession(sessionId: string, actor: AuthUser): Promise<void>`
**Purpose:** Signs one device out (`POST /auth/logout` revokes the current one). Its access token stops working at once and its sockets close.
**Exceptions:** `notFound` `AUTH.SESSION_NOT_FOUND` (404) for another user's or an unknown session.

### `revokeAllSessions(userId: string): Promise<void>`
**Purpose:** Signs every device out (`POST /auth/logout-all`, password change, deactivation).
