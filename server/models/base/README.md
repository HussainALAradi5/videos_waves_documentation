# Base entity (`baseEntityPlugin`)

`models/base/base-entity.model.ts`. Every schema calls `schema.plugin(baseEntityPlugin, { codePrefix })`. The plugin adds:

| Field | Type | Rule |
| --- | --- | --- |
| `_id` (`id` in DTOs) | ObjectId | Primary key |
| `code` | string | Required, **unique index**, immutable. Generated as `PREFIX-XXXXXXXX` by `generateCode` (8 characters from an unambiguous alphabet) |
| `createdAt`, `updatedAt` | Date | Mongoose timestamps |
| `createdBy` | ObjectId → User | Immutable. Set by `BaseRepository.create(data, actorId)` |
| `updatedBy` | ObjectId → User | Set on every repository update and soft delete |
| `deletedAt` | Date \| null | Indexed. `BaseRepository` excludes rows where it is set |
| `deletedBy` | ObjectId → User \| null | Who soft-deleted the row |

Code prefixes: `USR` user, `SES` session, `SET` settings, `FOL` follow, `BLK` block, `RST` restriction, `ADR` admin request, `MED` media, `VID` video, `LIK` like, `CMT` comment, `CNV` conversation, `CMB` conversation member, `MSG` message, `RCT` reaction, `MIG` migration.

Response DTOs share `BaseEntityResponseDto` (`id`, `code`, `createdAt`; audit ids stay server-side) from `dtos/common/base/base-entity-response.dto.ts`.

Self-registration pre-generates the user's `_id`, so `createdBy` is the new user.
