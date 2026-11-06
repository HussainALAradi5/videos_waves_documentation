# Video service

`services/videos/video/video.service.ts`

### `findVideoOrFail(videoId: string, viewerId?: string): Promise<Video>`
Loads an active video or throws `notFound` `VIDEOS.NOT_FOUND` (404). With a viewer, a video whose owner blocked them, or whom they blocked, also looks missing. Shared by every video, like, comment, and shared-video workflow.

### `listVideos(query: VideoQueryDto, viewerId?: string): Promise<PageResult<VideoResponseDto>>`
**Business logic:** Hides owners in `getHiddenUserIds`. `scope=following` keeps only owners the viewer follows. `ownerId` lists one person's videos (profile grid). `search` matches the title (escaped, case-insensitive). `isLiked` is resolved for the whole page in one query.

| Exception | When | HTTP |
| --- | --- | --- |
| `unauthorized` `SOCIAL.FOLLOWING_REQUIRED` | `scope=following` as a guest | 401 |

### `createVideo(dto: CreateVideoDto, actor: AuthUser): Promise<VideoResponseDto>`
**Business logic:** In one transaction: `claimMedia([dto.mediaId], video)`, create the video, `videosCount + 1`. The client uploaded the file to the CDN first (see [media](../../media/media/README.md)).
**Exceptions:** 400/409 from `claimMedia`.

### `updateVideo(videoId, dto, actor)`
Title and description only (strict DTO). Owner or admin (`assertCanManage`): 403, 404.

### `deleteVideo(videoId: string, actor: AuthUser): Promise<void>`
**Business logic:** Owner or admin. In one transaction: soft-delete the video and its comments, remove its likes, release its media, `videosCount - 1`. After the commit, purge the CDN file.
**Exceptions:** 403, 404.

### `recordView(videoId)`
`viewsCount + 1` (clients call it once per visit on first play). 404.
