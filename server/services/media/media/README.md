# Media and storage services

The server never receives file bytes in production: clients upload straight to ImageKit with a short-lived signature, then register the file. See [integrations → ImageKit](../../../../integrations/README.md).

## Storage adapters (`services/media/storage/`)

Both implement `MediaStorage` (`interfaces/media/storage/media-storage.interface.ts`): `createUploadSignature`, `getFileDetails`, `deleteFile`, `getSignedUrl`, `buildUrl`. `globals/media/media-storage.globals.ts` picks one from `MEDIA_PROVIDER`.

| Adapter | When | Notes |
| --- | --- | --- |
| `imagekit-storage.service.ts` | `MEDIA_PROVIDER=imagekit` | `@imagekit/nodejs`; startup fails fast when a key is missing; the private key stays on the server |
| `local-storage.service.ts` | `local` (default for development and tests) | Mimics ImageKit's upload endpoint at `POST /media/local-upload`, stores under `MEDIA_LOCAL_DIR`, and signs private delivery URLs with `MEDIA_LOCAL_SECRET` |

## Media service (`services/media/media/media.service.ts`)

### `createUploadSignature(kind: MediaKind, actor: AuthUser): Promise<UploadSignatureResponseDto>`
**Purpose:** Step 1 of an upload. Returns the provider URL, public key, token, expiry (30 minutes), signature, the folder `/videos-waves/<kind>/<userId>`, `isPrivateFile`, `maxBytes`, and the allowed `mimeTypes` for that kind.

| Kind | Types | Max | Visibility |
| --- | --- | --- | --- |
| `video` | mp4, webm, quicktime | `UPLOAD_MAX_VIDEO_MB` (≤ 100 MB, the ImageKit free-plan limit) | public |
| `avatar` | jpeg, png, webp, gif | 5 MB | public |
| `chat_attachment` | images, videos, pdf | 25 MB | private |
| `voice_note` | webm, ogg, mp4/m4a, mpeg, aac | 10 MB | private |

### `registerMedia(dto: RegisterMediaDto, actor: AuthUser): Promise<MediaResponseDto>`
**Purpose:** Step 3. Reads the file back from the provider and checks that it is in the actor's folder for that kind, has an allowed type, and is under the size limit. Only then is it recorded as `pending`. A file that breaks a rule is **deleted from the provider**, so it cannot linger or be reused.

| Exception | When | HTTP |
| --- | --- | --- |
| `notFound` `MEDIA.FILE_NOT_UPLOADED` | The provider has no such file | 404 |
| `conflict` `MEDIA.ALREADY_USED` | The file is already registered | 409 |
| `badRequest` / 413 / 415 | Wrong folder, size, or type | 400, 413, 415 |

### `claimMedia(mediaIds: string[], kind: MediaKind, actor: AuthUser): Promise<Media[]>`
**Purpose:** Marks the actor's pending uploads `attached` for one use. Call inside the transaction that creates the owning video, avatar, or message, so a failed save leaves them pending.
**Exceptions:** 400 `MEDIA.WRONG_KIND`; 409 `MEDIA.ALREADY_USED` (missing, someone else's, or already attached).

### `releaseMedia(mediaIds, actorId)` and `deletePendingMedia(mediaId, actor)`
`releaseMedia` soft-deletes inside the caller's transaction. `deletePendingMedia` lets the uploader discard an unused upload (removed from the picker): 404 `MEDIA.NOT_FOUND`, 409 `MEDIA.ALREADY_USED`.

## Cleanup (`media-cleanup.service.ts`)

| Function | Purpose |
| --- | --- |
| `purgeProviderFiles(mediaIds?)` | Deletes CDN files of soft-deleted media after a commit. Best effort: a failure is logged and retried by `npm run media:cleanup` |
| `removeStaleUploads()` | Removes pending uploads older than 24 hours |

## Local delivery (`local-media.service.ts`)

`saveLocalUpload(file, dto)` verifies the upload signature (401 `MEDIA.UPLOAD_SIGNATURE_INVALID`, 400 `MEDIA.FILE_REQUIRED`) and is refused unless the provider is `local` (403 `MEDIA.LOCAL_PROVIDER_ONLY`). `resolveLocalFile(path, expires, signature)` serves only active media, and private files need a valid, unexpired signature (403 `MEDIA.DELIVERY_SIGNATURE_INVALID`, 404).
