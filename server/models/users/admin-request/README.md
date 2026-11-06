# AdminRequest

| Field | Type | Rules |
| --- | --- | --- |
| `requester` | ObjectId → User | Required |
| `reason` | string | 10–500 characters (DTO), trimmed |
| `status` | `AdminRequestStatus` | `pending` (default) → `accepted` \| `rejected` \| `cancelled` |
| `decidedBy` | ObjectId → User \| null | Admin who decided |
| `decidedAt` | Date \| null | |
| `decisionNote` | string \| null | ≤ 500 characters |

## Indexes
- `{ requester, createdAt: -1 }`, `{ status, createdAt: -1 }`
- `one_pending_request_per_user`: unique on `requester` with `partialFilterExpression: { status: 'pending' }`. The database itself allows at most one pending request per user.

## Workflow
`pending → accepted` (admin; promotes the requester to `admin`), `pending → rejected` (admin), and `pending → cancelled` (the requester). Every transition is one atomic `findOneAndUpdate` filtered on `status: pending`, so two admins cannot both decide the same request.
