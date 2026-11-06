# Phase 07: Mobile

**Status:** Done in round 1; rebuilt in round 2 ([phase 12](../phase-12-ui-redesign/README.md))
**Depends on:** Phase 04

## Goal
A Flutter app on the same API with the core use cases.

## Round 1 scope
Feed with search and infinite paging, video details with playback, likes and comments, upload from the device, sign in and register, profile with an admin request, and admin review.

## Round 1 verification
| Check | Command | Result |
| --- | --- | --- |
| Static analysis | `flutter analyze` | No issues |
| Tests | `flutter test` | 16 passed |
| Android build | `flutter build apk --debug` | Built `app-debug.apk` (after setting `kotlin.incremental=false`) |
| Runtime (web build at 375–1024 px) | `flutter build web --dart-define=API_BASE_URL=http://localhost:5000` | Feed, details with streaming, guest like → sign-in → return, like, and comment |

The round 2 rebuild and its verification are in [phase 12](../phase-12-ui-redesign/README.md).

## Limits
- Never run on an Android emulator, an iOS simulator, or a physical device; the iOS build was not attempted (it needs macOS).
