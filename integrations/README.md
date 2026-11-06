# Integrations

## ImageKit (media CDN)

Every uploaded file (videos, thumbnails, profile pictures, chat attachments, voice notes) is stored on and delivered by [ImageKit](https://imagekit.io). The API server stores only metadata, so hosting the demo does not need disk space or bandwidth for video.

### Upload flow

```text
client ── POST /media/signature { kind } ─────────────► API   (token, expiry, signature, folder, rules)
client ── multipart upload with progress ─────────────► ImageKit upload API
client ── POST /media { fileId, kind, originalName } ─► API ── reads the file back from ImageKit, checks folder, type, size
client ── POST /videos { mediaId, … } (or avatar, message) ► API ── claims the media inside the transaction
```

- The private key exists only in the server `.env`. Signatures expire after 30 minutes and are scoped to `/videos-waves/<kind>/<userId>`.
- A file that breaks a rule is deleted from ImageKit at registration.
- Videos, thumbnails (`…/ik-thumbnail.jpg`), and avatars are public URLs. Chat attachments and voice notes are **private**: each message response carries signed URLs valid for one hour, created at read time.
- Deleting a video, unsending a message, or replacing an avatar deletes the ImageKit file after the database commit. Failures are retried by `npm run media:cleanup`.

### Free plan limits (imagekit.io/plans, checked 2026-11-06)

| Limit | Free plan | What it means here |
| --- | --- | --- |
| Delivery bandwidth | 20 GB / month | The first limit a video demo reaches |
| Storage | 3 GB | Roughly 30 videos at the 100 MB maximum, or many more short clips |
| Upload size | Videos 100 MB; images, audio, other files 25 MB | Enforced by the server: `UPLOAD_MAX_VIDEO_MB` ≤ 100; avatars 5 MB, chat files 25 MB, voice notes 10 MB |
| Video processing | 500 units / month | Thumbnails and streaming variants |
| Users | 2 seats | Dashboard access |
| Over the limit | Not stated for the free plan | Plan for the Lite plan ($9 / month: 40 GB bandwidth, 10 GB storage, then pay as you go) if the demo gets traffic |

### Configuration

| Variable | Value |
| --- | --- |
| `MEDIA_PROVIDER` | `imagekit` |
| `IMAGEKIT_PUBLIC_KEY`, `IMAGEKIT_PRIVATE_KEY`, `IMAGEKIT_URL_ENDPOINT` | From the ImageKit dashboard → Developer options |

The server refuses to start with `MEDIA_PROVIDER=imagekit` and a missing key. Without an account, keep `MEDIA_PROVIDER=local`: files are written to `MEDIA_LOCAL_DIR` and served by the API. That is for development only.

**Status:** the ImageKit adapter is implemented, but a real upload has **not** been verified, because no account keys have been configured yet. Automated tests and the browser checks used the local provider.
