# Phase 05: Client foundation

**Status:** Done
**Depends on:** Phase 04

## Goal
A Next.js client wired to the API through one configured HTTP client.

## Scope
| Layer | Folders | Items |
| --- | --- | --- |
| Config | `src/config/` | API base URL from `NEXT_PUBLIC_API_BASE_URL`, endpoint map mirroring server routes, query client, toaster |
| Services | `src/services/` | Axios client, envelope unwrapping, token storage, a service per resource |
| Middlewares | `src/middlewares/` | Auth interceptor (single-flight refresh), error interceptor, `RequireAuth` guard |
| Providers | `src/providers/` | Chakra, next-themes, TanStack Query, auth session |
| Generic components | `src/components/common/` | Forms, dialogs, grid, table, pagination, states, badges, dropzone |

## Verification
| Check | Command | Result |
| --- | --- | --- |
| Typecheck | `npm run typecheck` | Passed |
| Lint | `npm run lint` | Passed, no warnings |
| Build | `npm run build` | Passed; 8 routes |
| Sign in against the running server | Browser | Passed |
