# Phase 11: Chat

**Status:** Done
**Depends on:** Phase 10

## Goal
Real-time chat that improves on the e_clinic chat: direct and group conversations, message requests, attachments, voice notes, reactions, replies, and Seen, with block and restrict rules.

## Tasks
| Task | Result |
| --- | --- |
| Models: Conversation, ConversationMember, Message, MessageReaction | Done |
| Direct chats (one per pair), requests, accept and delete | Done |
| Groups: create, rename, add, remove, leave, promote, demote, last-admin rule, 32-person cap, system notices | Done |
| Messages: text, up to 10 private attachments, voice notes, shared videos, replies, 15-minute edit, unsend, delete for me | Done |
| Reactions (one per person, allowlist) | Done |
| Socket.IO: JWT handshake, per-user rooms, events after commit, typing, presence, Seen with privacy rules | Done |
| Cursor pagination for threads; page pagination for the inbox | Done |

## Acceptance criteria
- Outsiders get 403 on every conversation route.
- Blocked pairs cannot start or send; restricted senders land in requests and never see Seen or presence.
- A rolled-back message is never emitted.

## Verification
| Check | Result |
| --- | --- |
| Server tests (`tests/services/chat/**`, `tests/controllers/chat/**`) | Included in the 77 passing tests: pairing, requests, block and restrict, group admin rules, edit window, unsend, reactions, outsiders |
| Web, two users in two tabs | Live message, reply, reaction, typing, Seen |
| Mobile web build | Start a chat from a profile, send, inbox row updates |

## Risks
- Presence is per process; scaling out needs the Redis adapter.
- Voice notes and attachments on mobile were not exercised in the running app (see phase 12).
