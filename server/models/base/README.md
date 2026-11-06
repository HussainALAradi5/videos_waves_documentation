# Base entity (`baseEntityPlugin`)

Every schema calls `schema.plugin(baseEntityPlugin, { codePrefix })`. The plugin adds:

| Field | Type | Rule |
| --- | --- | --- |
| `_id` (`id` in DTOs) | ObjectId | Primary key |
| `code` | string | Required, **unique index**, immutable. Generated as `PREFIX-XXXXXXXX` by `generateCode` (8 characters from an unambiguous alphabet) |
| `createdAt`, `updatedAt` | Date | Mongoose timestamps |
| `createdBy` | ObjectId → User | Immutable. Set by `BaseRepository.create(data, actorId)` |
| `updatedBy` | ObjectId → User | Set on every repository update and soft delete |
| `deletedAt` | Date \| null | Indexed. `BaseRepository` excludes rows where it is set |
| `deletedBy` | ObjectId → User \| null | Who soft-deleted the row |

Code prefixes: `USR` user, `VID` video, `LIK` like, `CMT` comment, `ADR` admin request.

Self-registration pre-generates the user's `_id`, so `createdBy` is the new user.
