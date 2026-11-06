# Phase 06: Client screens

**Status:** Done
**Depends on:** Phase 05

## Goal
Every use case working in the browser.

## Scope
| Route | View | Access |
| --- | --- | --- |
| `/` | Feed with search, sort, infinite scroll | Public |
| `/videos/[videoId]` | Player, likes, owner actions, comments | Public (actions need sign-in) |
| `/upload` | Drag-and-drop upload with progress | Signed in |
| `/login`, `/register` | Auth forms | Public |
| `/profile` | Profile, edit, password, deactivate, admin request, my videos | Signed in |
| `/admin/requests` | Review requests (filter, page, accept or reject) | Admin |

## Verification (browser, 2026-11-06)
| Flow | Result |
| --- | --- |
| Register with a weak password | Inline rule message shown, no request sent |
| Register → redirected home, Upload link appears | Passed |
| Upload a WebM via the dropzone | Redirected to the new video page |
| Comment, like, play (view counted) | Counters show 1/1/1 |
| Profile → request admin | Request shown as Pending with Cancel |
| Sign out → open `/admin/requests` | Redirected to `/login?redirect=%2Fadmin%2Frequests` |
| Sign in as seeded admin → accept | Request leaves the Pending list; requester's role is `admin` |
| Mobile width (375 px), light mode | Layout checked |

All component, page, and hook files are under 150 lines; the longest is 72.
