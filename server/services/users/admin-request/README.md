# Admin request service

### `createAdminRequest(dto: CreateAdminRequestDto, actor: AuthUser): Promise<AdminRequestResponseDto>`

**Purpose:** A user asks to become an admin.

**Business logic:** Rejects admins, rejects a second pending request (the unique partial index also guarantees this), and saves the request as `pending`.

**Cases covered:**
| Case | Result |
| --- | --- |
| User without a pending request | 201, `pending` |
| A pending request exists | Rejected |
| The actor is already an admin | Rejected |

**Exceptions:**
| Exception | When | HTTP status |
| --- | --- | --- |
| `conflict` | Already admin, or a pending request exists | 409 |

### `listAdminRequests(query: AdminRequestQueryDto, actor: AuthUser): Promise<PageResult<AdminRequestResponseDto>>`

**Purpose:** Paged requests. Admins see all of them; other users see only their own. Optional `status` filter.

**Exceptions:** None.

### `getAdminRequest(requestId: string, actor: AuthUser): Promise<AdminRequestResponseDto>`

**Purpose:** One request, readable by its requester or an admin.

**Exceptions:**
| Exception | When | HTTP status |
| --- | --- | --- |
| `notFound` | Missing | 404 |
| `forbidden` | Neither the requester nor an admin | 403 |

### `decideAdminRequest(requestId: string, dto: DecideAdminRequestDto, actor: AuthUser): Promise<AdminRequestResponseDto>`

**Purpose:** An admin accepts or rejects a pending request. The route requires the `admin` role.

**Business logic:**
1. Atomically moves `pending → accepted | rejected` and stores `decidedBy`, `decidedAt`, and `decisionNote`.
2. If the request was not pending, reports `conflict` (or `notFound` if it does not exist).
3. On `accepted`, sets the requester's `role` to `admin`. The change applies on their next request, because `authenticate` reloads the role.

**Exceptions:**
| Exception | When | HTTP status |
| --- | --- | --- |
| `notFound` | Missing | 404 |
| `conflict` | Already decided or cancelled | 409 |

### `cancelAdminRequest(requestId: string, actor: AuthUser): Promise<AdminRequestResponseDto>`

**Purpose:** The requester withdraws a pending request (`pending → cancelled`). Admins cannot cancel on someone else's behalf.

**Exceptions:**
| Exception | When | HTTP status |
| --- | --- | --- |
| `notFound` | Missing | 404 |
| `forbidden` | Not the requester | 403 |
| `conflict` | No longer pending | 409 |
