# Models

| Model | File | Collection |
| --- | --- | --- |
| [Base entity](base/README.md) | `models/base/base-entity.plugin.ts` | — (a plugin) |
| [User](users/user/README.md) | `models/users/user/user.model.ts` | `users` |
| [AdminRequest](users/admin-request/README.md) | `models/users/admin-request/admin-request.model.ts` | `adminrequests` |
| [Video](videos/video/README.md) | `models/videos/video/video.model.ts` | `videos` |
| [VideoLike](videos/video-like/README.md) | `models/videos/video-like/video-like.model.ts` | `videolikes` |
| [Comment](videos/comment/README.md) | `models/videos/comment/comment.model.ts` | `comments` |

## Relationships

| Model | Inherits | Relation | Target | Foreign key | On delete |
| --- | --- | --- | --- | --- | --- |
| Video | BaseEntity | many-to-one | User (owner) | `owner` | User soft delete keeps the video |
| VideoLike | BaseEntity | many-to-one | Video | `video` | Hard-deleted when the video is soft-deleted |
| VideoLike | BaseEntity | many-to-one | User | `user` | — |
| Comment | BaseEntity | many-to-one | Video | `video` | Soft-deleted with the video |
| Comment | BaseEntity | many-to-one | User (author) | `author` | — |
| AdminRequest | BaseEntity | many-to-one | User (requester) | `requester` | — |
| AdminRequest | BaseEntity | many-to-one | User (decider) | `decidedBy` | — |
| User | BaseEntity | one-to-many | Video, Comment, VideoLike, AdminRequest | — | Soft delete only |

Video ↔ User likes form a many-to-many relation through `VideoLike`, with a unique `(video, user)` index.

## Counters

`Video.likesCount`, `commentsCount`, and `viewsCount` are denormalized counters. They change with atomic `$inc`, never by reading and writing back. A decrement is guarded with `{ counter: { $gte: n } }` so the value never goes below zero. A like increments only when the upsert actually inserted a row, and an unlike decrements only when a row was deleted. That keeps likes idempotent without a multi-document transaction (so a replica set is not required).
