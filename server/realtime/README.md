# Realtime (Socket.IO)

Socket.IO 4 shares the HTTP server (`config/realtime/socket.config.ts`, attached in `server.ts`) at path `/socket.io`, with the same CORS origins.

## Connection

1. The client connects with `auth: { token: <accessToken> }`. `middlewares/realtime/socket-auth.middleware.ts` runs `resolveAccessToken`, the same check as HTTP. A bad or revoked token refuses the connection.
2. `handleConnection` joins the room `user:<id>`, so every device of the user receives events, and marks the user online.
3. On reconnect, clients send the latest access token (web: `socketClient.auth`; mobile: `onReconnectAttempt`).
4. Revoking a session (`logout`, reuse detection) closes that session's sockets.

## Events

| Event | Direction | Payload | Sent when | Who receives it |
| --- | --- | --- | --- | --- |
| `message:new` | server → client | `MessageResponseDto` | A message was committed | Active members |
| `message:updated` | server → client | `MessageResponseDto` | Edit or unsend | Active members |
| `message:reaction` | server → client | `{ conversationId, messageId, userId, emoji \| null, reactions: [{ emoji, count }] }` | React or remove | Active members |
| `conversation:updated` | server → client | `{ conversationId }` | New message, rename, members, roles, accept, read by me | Members (and removed people) |
| `conversation:read` | server → client | `{ conversationId, userId, lastReadAt }` | Someone read the chat | Others, except people the reader restricted; not sent when read receipts are off |
| `typing` | client → server | `{ conversationId, isTyping }` | Composer has text (true) or cleared or sent (false) | — |
| `typing` | server → client | `{ conversationId, userId, isTyping }` | Relayed | Other members, except people who restricted the typer |
| `presence` | server → client | `{ userId, isOnline, presence, statusText }` | First socket opened or last closed, or the user changed `presence`, `statusText`, or activity status | Direct-chat partners, unless activity status is off or they are restricted |
| `notification:new` | server → client | `NotificationResponseDto` | A notification was stored | The recipient's devices |

Clients apply these straight to their caches: web through `utilities/cache/chat-cache.utility.ts` (TanStack Query), mobile through `ThreadSocketBinding`, `InboxController`, and `ChatSocketController`.
