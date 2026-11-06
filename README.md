# Videos Waves — Documentation

Videos Waves is a short-video sharing app. Users upload videos, browse an infinite feed, like and comment, and can ask to become admins, who moderate comments and videos and review those requests.

This repository holds the project documentation. The code lives in three separate repositories:

| Repository | Stack | Path |
| --- | --- | --- |
| Server | Express 5, TypeScript, MongoDB (Mongoose 9), Zod 4, JWT | [../videos_waves_server](../videos_waves_server) |
| Web client | Next.js 16 (App Router), React 19, Chakra UI 3, TanStack Query 5 | [../videos_waves_client](../videos_waves_client) |
| Mobile | Flutter 3.47, provider, go_router, Dio | [../videos_waves_mobile](../videos_waves_mobile) |

The original monorepo (`videos_waves`, remote `HussainALAradi5/videos_waves`) is the parent folder. The server and client repositories were split from it with `git subtree split`, so each keeps its own history.

## Contents

| Section | What it covers |
| --- | --- |
| [Phases](phases/README.md) | The nine project phases, their status, and verification |
| [Architecture and API contract](architecture/README.md) | System diagram, response envelope, pagination, errors, auth flow |
| [Server](server/README.md) | Models, services, controllers, enums, constants, and globals, mirroring `src/` |
| [Web client](client/README.md) | Services, hooks, generic components, config |
| [Mobile](mobile/README.md) | Controllers, services, widgets, config |
| [Use cases](use-cases/README.md) | What happens for each actor and flow |
| [Security](security/README.md) | Auth, uploads, permissions, and the findings fixed during the refactor |
| [Setup](setup/README.md) | Running each repository locally |

## Roles

| Role | Can do | Seeded development account |
| --- | --- | --- |
| `admin` | Everything a user can, plus delete any video or comment, list users, and accept or reject admin requests | `admin` / `admin@videos-waves.test` |
| `user` | Upload, edit, and delete own videos; like; comment; edit and delete own comments; request admin access | `viewer` / `viewer@videos-waves.test` |

Seed passwords come from `SEED_ADMIN_PASSWORD` and `SEED_USER_PASSWORD`, or are generated into the server's ignored `.seed-credentials.local.json`. Seeding refuses to run in production.

## Screenshots

Screenshots belong in the web client repository. Older screenshots of the original Vite UI are in the parent monorepo's `images/` folder.
