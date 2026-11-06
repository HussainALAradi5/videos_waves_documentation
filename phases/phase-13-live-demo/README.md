# Phase 13: Live demo and admin user management

**Status:** Ready to deploy. Nothing is deployed yet: the hosting accounts have to be created first (see [deployment](../../deployment/README.md)).
**Depends on:** Phase 12

## Tasks
| Task | Result |
| --- | --- |
| Demo seed: two admins and four users, follows, comments with owner replies, a direct chat, a group with a reaction, and a message request | Done, through the real services; idempotent |
| Production seeding only with `ALLOW_DEMO_SEED` and configured passwords; every start restores changed demo passwords and reactivates demo accounts | Done |
| Render Blueprint and compiled `migrate:prod` and `seed:prod` | Done |
| GitHub Actions: Flutter web on GitHub Pages and a release APK artifact | Done (runs once Pages is enabled and `API_BASE_URL` is set) |
| Android release permissions (internet, microphone) and iOS usage descriptions | Done; the release APK had no internet permission before |
| Admin activate and deactivate, as a reversible status that hides content | Done: server, web, mobile |
| Admin users page: table, badges, search, filters, pagination | Done: web and mobile |
| CSV export and import | Done: web |

## Verification
| Check | Result |
| --- | --- |
| Server | `npm run lint`, `npm run typecheck`, `npm test`: 87 passed in 18 files (demo seed, account status, hidden content, export, import) |
| Production dry run | Compiled build with `NODE_ENV=production` against a throwaway replica set: migrations, the seed twice (the second run changed nothing), API start, seeded admin sign-in, and the feed with each owner |
| Web | Typecheck, lint, `npm test` (13 passed); in the browser as the seeded admin: the users table and badges, deactivate (the feed then hides that user's video) and reactivate, search, and CSV import with a preview, a skipped duplicate, and the result |
| Mobile | `flutter analyze` clean, `flutter test` (24 passed), release APK built (57.8 MB), web build showing the admin users screen with badges and filters |

## Not verified
- The status dropdown filter and the Export download were not clicked in the browser (the pane cannot open menus or save files while hidden); the server tests cover both, and the dropdown component is used elsewhere.
- Nothing is deployed, and the GitHub workflow has not run yet.

## Release check (2026-11-06)
| Check | Result |
| --- | --- |
| Builds and static checks | Server lint, typecheck, build; web lint, typecheck, build (14 routes); mobile analyze, web build; `npm audit --omit=dev`: 0 vulnerabilities in server and web |
| Tests | Server 93 passed, web 13, mobile 24 |
| Contract | Every web endpoint (43 paths) and mobile endpoint (42, literal and composed) exists on the server |
| End to end, live API | 58 checks: health and errors, register with phone, duplicates and weak passwords, login by email in any case, refresh rotation, sessions, roles and 403s, paging limits, direct upload and publish, media reuse (fixed to return 409), likes, views, comments, owner replies, edit permissions, delete and undo, follow, partial search by username, email, and phone, chat send, reaction, outsider 403, unsend, admin deactivate and reactivate, CSV export, admin request accept, logout |
| Browser | Web (production build) and mobile web: feed, video page with the Owner badge, chat with a reaction, chat options (mute for 8 hours, unmute, delete chat), profile, light and dark |
| Fixed during the check | Media already used now returns 409 (was a misleading 400); the comments header counts replies, matching the feed card; the emoji bar closes after picking |
