# Enums

| Enum | File | Values | Used by |
| --- | --- | --- | --- |
| `Role` | `enums/users/roles/role.enum.ts` | `admin`, `user` | `User.role`, `authorize()`, ownership checks |
| `AdminRequestStatus` | `enums/users/admin-request/admin-request-status.enum.ts` | `pending`, `accepted`, `rejected`, `cancelled` | `AdminRequest.status`, the decision DTO (`accepted` \| `rejected` only) |
| `TokenType` | `enums/users/auth/token-type.enum.ts` | `access`, `refresh` | JWT `type` claim |
| `ErrorCode` | `enums/common/error-code.enum.ts` | See [error codes](../../architecture/README.md#error-codes) | `AppError`, the error handler, both clients |
| `SortDirection` | `enums/common/sort-direction.enum.ts` | `asc`, `desc` | Page requests |

The web client mirrors these in `src/enums/` (plus a client-only `NETWORK_ERROR`), and the mobile app in `lib/enums/`. Labels and colors for `Role` and `AdminRequestStatus` live in one map per client.
