# Videos Waves — Documentation

Videos Waves is a short-video sharing app in the style of Instagram. People upload videos, browse a For you or Following feed, like, comment and reply, follow each other, and chat live in direct and group conversations with photos, files, voice notes, reactions, and Seen. Block and restrict work as on Instagram. Admins moderate content and review requests for admin access.

This repository holds the project documentation. The code lives in three separate repositories:

| Repository | Stack | Path |
| --- | --- | --- |
| Server | Express 5, TypeScript, MongoDB (Mongoose 9, replica set), Zod 4, JWT sessions, Socket.IO 4, ImageKit | [../videos_waves_server](../videos_waves_server) |
| Web client | Next.js 16 (App Router), React 19, Chakra UI 3, TanStack Query 5, motion, socket.io-client | [../videos_waves_client](../videos_waves_client) |
| Mobile | Flutter 3.47, provider, go_router, Dio, socket_io_client, record, just_audio | [../videos_waves_mobile](../videos_waves_mobile) |

On GitHub: [server](https://github.com/HussainALAradi5/videos_waves_server), [web client](https://github.com/HussainALAradi5/videos_waves_client), [mobile](https://github.com/HussainALAradi5/videos_waves_mobile), [documentation](https://github.com/HussainALAradi5/videos_waves_documentation).

## Contents

| Section | What it covers |
| --- | --- |
| [Phases](phases/README.md) | The twelve project phases, their status, and verification |
| [Architecture and API contract](architecture/README.md) | System diagram, envelope, pagination, errors, sessions, realtime |
| [Integrations](integrations/README.md) | ImageKit (media CDN): upload flow, limits, keys |
| [Server](server/README.md) | Models, services, controllers, realtime, enums, constants, globals, mirroring `src/` |
| [Web client](client/README.md) | Routes, services, hooks, components, realtime cache |
| [Mobile](mobile/README.md) | Controllers, services, widgets, screens, realtime |
| [Use cases](use-cases/README.md) | What happens for each actor and flow |
| [Security](security/README.md) | Controls, privacy rules, fixed findings, known limits |
| [Setup](setup/README.md) | Running and verifying each repository |

## Roles

| Role | Can do | Seeded development account |
| --- | --- | --- |
| `admin` | Everything a user can, plus delete any video or comment, list users, and accept or reject admin requests | `admin` / `admin@videos-waves.test` |
| `user` | Upload, edit, and delete own videos; like; comment and reply; delete comments on own videos; follow, block, restrict; chat; request admin access | `viewer` / `viewer@videos-waves.test` |

Seed passwords come from `SEED_ADMIN_PASSWORD` and `SEED_USER_PASSWORD`, or are generated into the server's ignored `.seed-credentials.local.json`. Seeding refuses to run in production. The seed's demo videos are public sample clips (MDN CC0 and Blender CC BY), linked rather than stored.

## Screenshots

Screenshots belong in the web client repository under `docs/screenshots/`. They have not been captured for the round 2 design yet; see [phase 12](phases/phase-12-ui-redesign/README.md#follow-ups).
