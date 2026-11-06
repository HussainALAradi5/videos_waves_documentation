# UserSettings

One row per user (`user` unique), created on first read with the defaults. Each field is a `SettingKey`.

| Key | Type | Default | Enforced where |
| --- | --- | --- | --- |
| `theme` | `system` \| `light` \| `dark` | `system` | Clients only (web and mobile apply it) |
| `autoplayVideos` | boolean | `true` | Clients only |
| `showActivityStatus` | boolean | `true` | Server: online status is sent as `null` to others when off |
| `readReceipts` | boolean | `true` | Server: `lastReadAt` is hidden from others when off |
| `messagePermission` | `everyone` \| `following` | `everyone` | Server: `startDirect` refuses people the user does not follow |
| `messagePreviews` | boolean | `true` | Clients only |
| `messageSound` | boolean | `true` | Web client only |
| `bubbleColor` | `wave` \| `ocean` \| `sunset` \| `forest` \| `mono` \| `neutral` | `wave` | Clients only: your own bubbles |
| `othersBubbleColor` | same values | `neutral` (theme surface) | Clients only: bubbles you receive |
| `bubbleStyle` | `rounded` \| `square` \| `minimal` | `rounded` | Clients only |
| `notifyMessages` | boolean | `true` | Clients only: pop-up for a message in a chat that is not open (never for muted chats) |
| `presence` | `online` \| `away` \| `busy` \| `invisible` | `online` | Server: members see `online`/`away`/`busy` while connected, `offline` otherwise; `invisible` always looks offline |
| `statusText` | string, at most 60 characters (trimmed) | `''` | Server: shown to chat partners as `statusText` unless activity status is off |
| `privateAccount` | boolean | `false` | Server: follows become requests; only approved followers see videos and follow lists. Switching to `false` accepts every pending request |

Changing `presence`, `statusText`, or `showActivityStatus` re-sends the `presence` event to chat partners at once.
