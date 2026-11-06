# Auth service

### `buildAuthResponse(user: User): AuthResponseDto`

**Purpose:** Issues tokens for a user and maps the user to `UserResponseDto`. Shared by register, login, and change password.

**Exceptions:** None.

### `assertIdentityAvailable(identity: { email?: string; userName?: string }, exceptId?: string): Promise<void>`

**Purpose:** Rejects an email or username that another account (including a deactivated one) already uses.

**Cases covered:**
| Case | Result |
| --- | --- |
| Both free, or used only by `exceptId` | Returns |
| Email taken | Rejected |
| Username taken | Rejected |

**Exceptions:**
| Exception | When | HTTP status |
| --- | --- | --- |
| `conflict` | Email or username in use | 409 |

### `register(dto: RegisterDto): Promise<AuthResponseDto>`

**Purpose:** Creates an account and signs it in.

**Business logic:**
1. `assertIdentityAvailable(dto)`.
2. Pre-generates the user id, so the new user is the `createdBy` actor.
3. Hashes the password with bcrypt (`BCRYPT_SALT_ROUNDS`) and saves the user with role `user`.
4. Returns `buildAuthResponse(user)`.

**Cases covered:**
| Case | Result |
| --- | --- |
| Valid input | 201 with a session |
| Duplicate email or username | Rejected |
| Weak password or invalid username (DTO) | 400 before the service runs |

**Exceptions:**
| Exception | When | HTTP status |
| --- | --- | --- |
| `conflict` | Identity in use | 409 |

### `login(dto: LoginDto): Promise<AuthResponseDto>`

**Purpose:** Signs in with a username or an email (any value containing `@` is treated as an email, lowercased).

**Business logic:** Loads the active user with secrets, compares the bcrypt hash, and returns a session. The same error is returned for an unknown user and a wrong password, so the response does not reveal whether an account exists.

**Exceptions:**
| Exception | When | HTTP status |
| --- | --- | --- |
| `unauthorized` | Unknown, deactivated, or wrong password | 401 |

### `refresh(dto: RefreshTokenDto): Promise<AuthTokensDto>`

**Purpose:** Exchanges a valid refresh token for a new token pair.

**Business logic:** Verifies the token as `refresh`, loads the user's `role` and `tokenVersion`, requires the versions to match, and issues tokens with the current role.

**Exceptions:**
| Exception | When | HTTP status |
| --- | --- | --- |
| `unauthorized` | Invalid or expired token, deactivated user, or revoked version | 401 |

### `logout(userId: string): Promise<void>`

**Purpose:** Revokes every token of the user by incrementing `tokenVersion`.

**Exceptions:** None.
