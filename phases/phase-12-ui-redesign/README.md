# Phase 12: UI redesign (web and mobile)

**Status:** Done, with the follow-ups below
**Depends on:** Phases 10 and 11

## Goal
A modern, animated, Instagram-style UI on both clients, built from generic components, covering every round 2 feature.

## Tasks
| Task | Web (Next.js, Chakra UI 3) | Mobile (Flutter) |
| --- | --- | --- |
| Brand theme, light and dark, theme from settings | Done | Done |
| Wave animations: backdrop, loader, rolling logo | Done | Done |
| Generic busy-safe buttons | `GenericButton` | `AppButton` |
| Custom dropdown family and ⋯ action menu | `SelectDropdown`, `MultiSelectDropdown`, `ActionMenu` | `AppDropdown`, `MultiSelectSheet`, `ActionSheetButton` |
| Multi-file picker with per-file progress | `MultiFileDropzone`, `FilePreviewTile` | `MultiFilePicker`, `FilePreviewTile` |
| Feed tabs, profiles, search, follow lists | Done | Done |
| Comments: replies, Owner badge, inline edit, delete with Undo, approve | Done | Done |
| Multi-video upload | Done | Done |
| Chat: inbox folders, requests, thread, attachments, voice, reactions, replies, typing, Seen | Done | Done |
| Groups: create | Done | Done |
| Groups: info and admin actions | Done | Not in the mobile UI (server and web only) |
| Settings, avatar, sessions, blocked and restricted lists | All | Settings, avatar, bio, admin request (no sessions or relation lists) |

## Verification
| Check | Result |
| --- | --- |
| Web | `npm run lint`, `npm run typecheck`, `npm test` (11 passed), `npm run build` (13 routes) |
| Web browser (desktop and 375 px, light and dark) | Register, profile, follow, live chat with reply and reaction, Owner badge, comment Undo, settings theme |
| Mobile | `flutter analyze` clean, `flutter test` (24 passed), `flutter build web` |
| Mobile web build in the browser (375 px) | Register with gender, feed tabs, Owner badge, post comment, delete with Undo (fixed during the check), follow, start a chat and send, inbox, settings dropdown switching to the light theme, rolling logo |

## Follow-ups
- **Not verified on mobile in the running app:** long-press reactions, attachments, voice notes, group creation, and multi-video upload.
- **Not run on a device:** no Android emulator, iOS simulator, or physical device; the round 2 APK was not rebuilt.
- **Screenshots** for the new design are not captured yet (they belong in the web client's `docs/screenshots/`).
- **Mobile gaps:** group info and admin actions, the sessions list, and blocked and restricted lists exist on the web only.
