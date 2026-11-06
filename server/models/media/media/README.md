# Media

One uploaded file on the CDN (ImageKit) or, in development, on local disk.

| Field | Type | Rules |
| --- | --- | --- |
| `kind` | `MediaKind` | `video`, `avatar`, `chat_attachment`, `voice_note` |
| `provider` | `MediaProvider` | `imagekit`, `local`, or `external` (seeded sample videos hosted elsewhere) |
| `visibility` | `MediaVisibility` | `public` (videos, avatars) or `private` (chat files, served through signed URLs) |
| `status` | `MediaStatus` | `pending` after upload, `attached` once claimed by a video, avatar, or message |
| `fileId` | string \| null | Provider file id |
| `filePath` | string | Path under `/videos-waves/<kind>/<userId>/` |
| `originalName`, `mimeType`, `sizeBytes` | | Read back from the provider, not trusted from the client |
| `width`, `height`, `durationSeconds` | number \| null | From the provider |
| `owner` | ObjectId → User | Only the owner can claim it |
| `providerDeletedAt` | Date \| null | Set once the CDN file is gone; the cleanup job retries rows without it |

Indexes: `{ filePath }`, `{ owner, status }`, `{ status, createdAt }` (stale pending uploads), `{ deletedAt, providerDeletedAt }` (purge retries), and a unique provider file index.

**Lifecycle:** signature → direct upload to the provider → `registerMedia` (`pending`) → `claimMedia` inside the owning record's transaction (`attached`) → `releaseMedia` (soft delete) → `purgeProviderFiles` after commit. Pending uploads older than 24 hours are removed by `npm run media:cleanup`.
