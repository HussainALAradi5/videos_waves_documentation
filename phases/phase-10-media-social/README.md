# Phase 10: Media and social graph

**Status:** Done. The ImageKit adapter has not been run against a real account yet (no keys configured).
**Depends on:** Phase 04

## Goal
Host every file on a free CDN instead of the API server, and add Instagram-style social features.

## Tasks
| Task | Result |
| --- | --- |
| Media domain: model, repository, service, storage interface with ImageKit and local adapters, signature and register endpoints, cleanup job | Done |
| Transactions for every multi-document write; replica-set test and dev databases | Done |
| Ordered migrations (`001-legacy-schema`, `002-sync-indexes`) and `npm run migrate` | Done |
| Base entity applied to every model; ESLint | Done |
| Follow, block, restrict with counters and shared visibility filter | Done |
| Profile, people search, followers and following | Done |
| Comments: one level of replies, Owner badge, Edited, restricted pending comments with Approve, delete with 15-second Undo, video owner may delete | Done |
| Per-device sessions with rotating refresh tokens, reuse detection, session list | Done |
| Settings by key, enforced on the server where they affect others | Done |
| Gender at registration and standard avatars; avatar upload | Done |

## Acceptance criteria
- No file bytes reach the API with `MEDIA_PROVIDER=imagekit`; files that break a rule are deleted at registration.
- A block hides both people from each other in every list and in chat.
- A restricted person's new comments are visible only to them and the video owner until approved.
- Refreshing from two tabs at once keeps both signed in; replaying an old refresh token revokes the session.

## Verification
| Check | Command | Result |
| --- | --- | --- |
| Lint, types, build | `npm run lint`, `npm run typecheck`, `npm run build` | Clean |
| Tests | `npm test` | 77 passed in 16 files (in-memory replica set) |
| Browser | In-memory API with the web client | Register with gender, profile, follow, comment with Owner badge, delete with Undo |

## Risks
- ImageKit free-plan bandwidth (20 GB / month) limits a public demo; see [integrations](../../integrations/README.md).
- `MONGO_URI` must be a replica set; a standalone MongoDB fails on the first transaction.
