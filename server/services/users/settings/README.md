# Settings service

`services/users/settings/settings.service.ts`. The keys are listed in the [UserSettings model](../../../models/users/settings/README.md).

| Function | Purpose |
| --- | --- |
| `getSettings(actor)` | Reads the user's row, creating it with the defaults on first use |
| `updateSettings(dto, actor)` | Partial update; the strict DTO accepts only known keys and values |
| `acceptsMessagesFrom(recipientId, senderId)` | `messagePermission = following` allows only people the recipient follows |
| `findUsersHiding(userIds, key)` | Of `userIds`, those with `showActivityStatus` or `readReceipts` turned off; chat views send `null` for them |
