# Admin requests

### UC-ADR-01 User requests admin access
- **Actor:** User (role `user`)
- **Preconditions:** Signed in; no pending request.
- **Main flow:** Profile → writes a reason (10–500 characters) → `POST /admin-requests` → the request appears as **Pending** with a Cancel button.
- **Alternate flows:** A pending request already exists → 409. The actor is already an admin → 409.
- **Result:** One `ADR-…` request with status `pending`.

### UC-ADR-02 User cancels their request
- **Main flow:** Cancel → `DELETE /admin-requests/:id` → status `cancelled` → the form is shown again.
- **Failure flow:** Already decided → 409.

### UC-ADR-03 Admin reviews requests
- **Actor:** Admin
- **Preconditions:** Signed in as an admin. Non-admins opening `/admin/requests` are redirected (and the API returns 403).
- **Main flow:** Opens the review list (pending by default; filter by status; paged) → Accept or Reject → `PATCH /admin-requests/:id/decision`.
- **Alternate flows:** Another admin decided first → 409 "This request has already been decided."
- **Result:** Accept: the request is `accepted` and the requester's role becomes `admin` on their next request. Reject: `rejected`. `decidedBy` and `decidedAt` are recorded either way.
