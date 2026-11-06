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
| `bubbleColor` | `wave` \| `ocean` \| `sunset` \| `forest` \| `mono` | `wave` | Clients only |
