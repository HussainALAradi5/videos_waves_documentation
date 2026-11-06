# Phase 14: Notifications, private accounts, status, and chat look

**Status:** done (local), not deployed.

## Tasks
- Server: notifications domain (model, repository, service, controller, routes, `notification:new`), follow requests and private accounts, Discover scope, `read` inbox filter, per-chat background, presence modes and custom status, unsend-until-read rule, new settings keys.
- Web: custom toast system (`ToastType` success, error, warning, info; slide-in; countdown bar that pauses on hover; queue of 4), unknown pages redirect home with a warning toast, notifications bell and page, follow requests, private profile lock, status picker, read ticks, right-click reactions, right-click chat options with a Mute submenu, chat backgrounds, bubble shape and colours with a live preview, pill tabs with a sliding gradient, dark theme polish.
- Mobile: the same toast system (overlay above every route), back-to-home on sign-in and sign-up, unknown routes go home with a warning, theme switch that also works for guests, bell and notifications screen, follow requests, private profile lock, status menu, ticks, chat look settings and preview, backgrounds, Mute as one row with a durations sheet, keyboard stays closed after option sheets.

- Unsend waits 5 seconds with an Undo toast (as on Instagram) before telling the server; the countdown does not pause.
- Demo data: `DEMO_SETTINGS` makes `maya` a private account (Away), `omar` Busy, `viewer` Online with a status line, and gives `lina` a square, Ocean/Forest chat look.

## Acceptance criteria
- Following a private account sends a request; content stays hidden until accepted.
- Every new follow, like, comment, reply, group add, and admin decision reaches the bell and arrives live.
- Unsend fails with 409 after any other member has read the message.
- Chat look settings are stored on the account and look the same on web and phone.

## Verification
- Server: 105 tests (privacy, notifications, chat look, status, unsend rule), lint, typecheck, build.
- Web: 18 tests (ticks, status line, bubble look, toast queue), lint, typecheck, production build; checked in the browser at desktop and phone widths, light and dark.
- Mobile: 28 tests, `flutter analyze`, debug APK checked on an Honor Magic 7 Pro (Android 16).

## Risks and limits
- Presence is kept in memory on one server process; several instances would need a shared store (Redis adapter).
- Notifications are in-app only (no push notifications to a closed phone app).
