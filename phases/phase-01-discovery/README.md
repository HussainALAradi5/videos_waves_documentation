# Phase 01: Discovery and research

**Status:** Done
**Depends on:** —

## Goal
Understand the original monorepo (Vite React client + JavaScript Express server), its data, and its gaps before splitting and rebuilding it.

## Findings
| Area | Original state | Decision |
| --- | --- | --- |
| Repositories | One repo with `client/` and `server/` | Split into server, client, mobile, and documentation repositories |
| Server | JavaScript Express 4, controllers held business logic and repeated token parsing | TypeScript Express 5 with layer-first folders |
| Client | Vite + React Router, hardcoded `http://localhost:5000`, components fetched data directly | Next.js App Router, one API client, hooks, generic components |
| Data | `User`, `Video` (likes in an array), `Comment`, `Request` | Base entity on every model; likes moved to a `VideoLike` collection |
| Roles | `isAdmin` boolean, admin requests never wired in | `role` enum (`admin`, `user`) and a full admin-request workflow |
| Mobile | None | New Flutter app |

## Models and roles agreed
`User`, `Video`, `VideoLike`, `Comment`, `AdminRequest`, each extending the base entity. Roles: `admin`, `user`. See [server models](../../server/models/README.md).

## Use cases
Register, sign in, refresh, sign out; browse and search the feed; watch (count a view); upload; edit or delete own video; like or unlike; comment, edit, or delete; request admin; admin reviews requests. See [use cases](../../use-cases/README.md).
