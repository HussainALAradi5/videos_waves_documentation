# Realtime services

`services/chat/realtime/*`. Events and payloads are listed in [server → realtime](../../../realtime/README.md).

| Function | Purpose |
| --- | --- |
| `emitToUsers(userIds, event, payload)` | Sends one event to the `user:<id>` room of each listed user (every device) |
| `disconnectSessions(userId, sessionIds?)` | Closes sockets of revoked sessions, or all of the user's sockets |
| `markConnected(userId)` / `markDisconnected(userId)` | In-memory connection counts; return true on the first open and last close |
| `isOnline(userId)` | Used by conversation views (respecting `showActivityStatus`) |
| `relayTyping(userId, event)` | Forwards `typing` to the other members if the sender is still a member, skipping people who restricted the sender |
| `broadcastPresence(userId, isOnline)` | Tells direct-chat partners, except restricted ones |

Presence is kept in process memory, so it is per server instance. Running several instances needs the Socket.IO Redis adapter and a shared presence store.
