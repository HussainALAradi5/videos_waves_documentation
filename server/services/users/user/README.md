# User service

### `getProfile(userId: string): Promise<UserResponseDto>`

**Purpose:** Returns the signed-in user.

**Exceptions:**
| Exception | When | HTTP status |
| --- | --- | --- |
| `notFound` | User missing or deactivated | 404 |

### `updateProfile(userId: string, dto: UpdateProfileDto): Promise<UserResponseDto>`

**Purpose:** Updates `userName`, `email`, and/or `image`. The DTO is strict: any other field, such as `role`, is rejected with 400.

**Business logic:** `assertIdentityAvailable(dto, userId)`, then `updateById` sets the fields and `updatedBy = userId`.

**Exceptions:**
| Exception | When | HTTP status |
| --- | --- | --- |
| `conflict` | Email or username in use | 409 |
| `notFound` | User missing | 404 |

### `changePassword(userId: string, dto: ChangePasswordDto): Promise<AuthResponseDto>`

**Purpose:** Changes the password and returns a new session for this device.

**Business logic:**
1. The DTO requires the new password to match its confirmation and the password rule.
2. Checks the current password against the stored hash.
3. Stores the new hash and increments `tokenVersion`, which revokes every other session.
4. Returns `buildAuthResponse`.

**Exceptions:**
| Exception | When | HTTP status |
| --- | --- | --- |
| `badRequest` | Current password incorrect | 400 |
| `notFound` | User missing | 404 |

### `deactivateAccount(userId: string): Promise<void>`

**Purpose:** Soft-deletes the account and revokes its tokens. The user can no longer sign in, and their identity cannot be reused.

**Exceptions:**
| Exception | When | HTTP status |
| --- | --- | --- |
| `notFound` | Already deactivated | 404 |

### `listUsers(query: UserQueryDto): Promise<PageResult<UserResponseDto>>`

**Purpose:** Paged list of active users for admins, with an optional `role` filter and a `search` over username or email (input is regex-escaped).

**Exceptions:** None (validation happens in the DTO).
