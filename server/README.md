# Server reference

These folders mirror `videos_waves_server/src/`:

| Section | Contents |
| --- | --- |
| [models](models/README.md) | Base entity, every model, relationships, indexes, counters |
| [services](services/README.md) | Every service function: signature, purpose, logic, cases, state changes, exceptions |
| [controllers](controllers/README.md) | Endpoint tables per controller |
| [realtime](realtime/README.md) | Socket.IO connection, rooms, and every event |
| [enums](enums/README.md) | Enum values and where they are used |
| [constants](constants/README.md) | Shared values and limits |
| [globals](globals/README.md) | Config, middlewares, utilities, repositories, migrations, conventions |

Request flow: `route → middlewares (requestId, logger, helmet, CORS, compression, rate limit, JSON, authenticate, authorize, validate) → controller → service → repository → model`. A mapper builds the response DTO and the response utility sends the envelope.

Writes that touch more than one document run inside `withTransaction` (`utilities/database/transaction.utility.ts`), so MongoDB must be a **replica set**. Atlas always is, and `npm run dev:memory` and the tests start an in-memory replica set. Operations inside one transaction run one after another: MongoDB refuses parallel operations on the same session.
