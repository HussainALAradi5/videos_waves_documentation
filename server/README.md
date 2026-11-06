# Server reference

These folders mirror `videos_waves_server/src/`:

| Section | Contents |
| --- | --- |
| [models](models/README.md) | Base entity, every model, relationships, indexes |
| [services](services/README.md) | Every service function: signature, purpose, logic, cases, exceptions |
| [controllers](controllers/README.md) | Endpoint tables per controller |
| [enums](enums/README.md) | Enum values and where they are used |
| [constants](constants/README.md) | Shared values and limits |
| [globals](globals/README.md) | Config, middlewares, utilities, and conventions |

Request flow: `route → middlewares (requestId, logger, helmet, CORS, compression, rate limit, JSON, authenticate, authorize, validate) → controller → service → repository → model`, then a mapper builds the response DTO and the response utility sends the envelope.
