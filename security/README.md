# Security

## Controls

| Area | Control | Where |
| --- | --- | --- |
| Passwords | bcrypt (12 rounds by default); `passwordHash` never selected or returned; login runs a dummy comparison for unknown accounts so timing does not reveal them | `utilities/security/password.utility.ts`, `auth.service.ts` |
| Sessions | One session per device; separate access and refresh secrets (≥ 32 characters), `type` claim, 15-minute access lifetime; rotating refresh tokens with reuse detection; per-device and all-device sign-out | `services/users/auth/session.service.ts`, `token.service.ts` |
| Authentication | Every request and socket handshake reloads role, token version, and session | `resolveAccessToken`, `authenticate`, `socket-auth` |
| Authorization | `authorize(Role.ADMIN)`; ownership checks in services (`assertCanManage`); chat membership on every chat call | routes, `validations/common/ownership.validation.ts`, `chat-access.service.ts` |
| Privacy | Blocks hide both people everywhere; restricted people get pending comments, land in requests, and never see read receipts or presence; activity status and read receipts can be turned off | `visibility.service.ts`, `restriction.service.ts`, `settings.service.ts` |
| Input | Zod DTOs on body, query, and params; strict update DTOs (no mass assignment); sort allowlists; regex-escaped search; ObjectId validation; reaction allowlist | `dtos/`, `validations/` |
| Media | Direct uploads with short-lived, folder-scoped signatures; the server re-reads type, size, and folder from the provider and deletes files that break a rule; each file can be claimed once by its owner; private chat files only through signed URLs that expire in 1 hour | `services/media/**` |
| Consistency | Multi-document writes in transactions; socket events only after commit | `utilities/database/transaction.utility.ts` |
| Transport | Helmet, CORS allowlist (HTTP and sockets), 1 MB JSON limit, rate limits | `app.ts`, `config/security/*` |
| Logging | Request id, method, path, status, duration only; never bodies, tokens, or passwords | `request-logger.middleware.ts` |
| Clients | Open-redirect guard on `?redirect=` (web `redirect.utility.ts`, mobile `safeRedirect`); tokens in secure storage on mobile | both clients |
| Secrets | Only `.env.example` files are committed; ImageKit's private key stays on the server | `.gitignore` |

## Findings fixed

Round 1 fixed thirteen findings in the original code (mass assignment, unlinked comments, unchecked uploads, open CORS, a non-idempotent like, readable admin requests, broken deletes, unbounded lists, an unreachable route, a single long-lived JWT, and inconsistent errors). Round 2 fixed:

| # | Finding | Fix |
| --- | --- | --- |
| 14 | Admin promotion was two writes without a transaction | `decideAdminRequest` accepts and promotes in one transaction |
| 15 | Deleted videos kept their files | Media released in the transaction, CDN file purged after commit, cleanup job retries |
| 16 | Sign-out revoked every device | Per-device sessions; `logout` revokes only the current one |
| 17 | Two tabs refreshing together could sign one out | Atomic rotation, a 30-second grace window, and Web Locks on the web |
| 18 | The web client cleared the session on any refresh failure, including network errors | Only a 401 from the refresh clears it |
| 19 | The mobile app cleared tokens when `me` failed offline | `restore` clears them only on 401 |
| 20 | Login timing revealed which accounts exist | Dummy bcrypt comparison |
| 21 | `?redirect=` could point outside the app | Same-origin path check on both clients |

## Known limits and follow-ups

- **Web token storage:** tokens are in `localStorage`, so an XSS bug could read them. React escapes output and no raw HTML is rendered. Moving the refresh token to an `httpOnly` cookie needs a same-site deployment and CSRF protection.
- **Presence** is kept in process memory. Several server instances need the Socket.IO Redis adapter and a shared store.
- **Release builds** must use HTTPS (Android blocks cleartext in release builds).
