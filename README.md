# Videos Waves — Documentation

Videos Waves is a short-video sharing app in the style of Instagram. People upload videos, browse a For you or Following feed, like, comment and reply, follow each other, and chat live in direct and group conversations with photos, files, voice notes, reactions, and Seen. Block and restrict work as on Instagram. Admins moderate content and review requests for admin access.

This repository holds the project documentation. The code lives in three separate repositories:

| Repository | Stack | Path |
| --- | --- | --- |
| Server | Express 5, TypeScript, MongoDB (Mongoose 9, replica set), Zod 4, JWT sessions, Socket.IO 4, ImageKit | [../videos_waves_server](../videos_waves_server) |
| Web client | Next.js 16 (App Router), React 19, Chakra UI 3, TanStack Query 5, motion, socket.io-client | [../videos_waves_client](../videos_waves_client) |
| Mobile | Flutter 3.47, provider, go_router, Dio, socket_io_client, record, just_audio | [../videos_waves_mobile](../videos_waves_mobile) |

On GitHub: [server](https://github.com/HussainALAradi5/videos_waves_server), [web client](https://github.com/HussainALAradi5/videos_waves_client), [mobile](https://github.com/HussainALAradi5/videos_waves_mobile), [documentation](https://github.com/HussainALAradi5/videos_waves_documentation).

## Live demo

| Part | Address |
| --- | --- |
| Web app | https://videos-waves-client.vercel.app |
| API (health) | https://videos-waves-api.onrender.com/health |
| Mobile web demo | https://hussainalaradi5.github.io/videos_waves_mobile/ |

Demo accounts: `admin` and `moderator` (admins), `viewer`, `lina`, `omar`, and `maya` (users; `maya` is a private account). Passwords are the `SEED_ADMIN_PASSWORD` and `SEED_USER_PASSWORD` values set on Render. The API runs on Render's free plan and sleeps after about 15 minutes idle; the first request after that takes up to a minute.

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
| [Deployment](deployment/README.md) | Free live demo: Atlas, ImageKit, Render, Vercel, GitHub Pages; demo accounts |

## Roles

| Role | Can do | Seeded development account |
| --- | --- | --- |
| `admin` | Everything a user can, plus delete any video or comment, list users, and accept or reject admin requests | `admin`, `moderator` |
| `user` | Upload, edit, and delete own videos; like; comment and reply; delete comments on own videos; follow, block, restrict; chat; request admin access | `viewer`, `lina`, `omar`, `maya` |

Each account's email is `<name>@videos-waves.test`. Admins share `SEED_ADMIN_PASSWORD` and users share `SEED_USER_PASSWORD`; in development missing passwords are generated into the server's ignored `.seed-credentials.local.json`. In production, seeding runs only for a demo (`ALLOW_DEMO_SEED=true`); see [deployment](deployment/README.md). The seed's demo videos are public sample clips (MDN CC0 and Blender CC BY), linked rather than stored.

## Screenshots

Real screenshots live with each client, in one section each:

- Web (desktop dark and light, tablet): [videos_waves_client → Screenshots](https://github.com/HussainALAradi5/videos_waves_client#screenshots)
- Mobile (375 px): [videos_waves_mobile → Screenshots](https://github.com/HussainALAradi5/videos_waves_mobile#screenshots)

## Known limits

- **Deployed:** Atlas (Frankfurt), ImageKit, Render, and Vercel on free plans; see [Live demo](#live-demo) and [deployment](deployment/README.md).
- **ImageKit** uploads have not been run against a real account; tests and checks used the local provider.
- **Render free plan** sleeps after 15 minutes; the first visit then takes about a minute.
- **Mobile** was checked as a web build and a release APK build, not on a phone or emulator.
- **Demo videos** have no thumbnail images on mobile (linked sample clips).
