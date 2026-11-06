# MessageReaction

| Field | Type | Rules |
| --- | --- | --- |
| `message`, `conversation`, `user` | ObjectId | Required |
| `emoji` | string | One of `❤️ 😂 😮 😢 😡 👍 🔥 👏` (`MESSAGE_REACTIONS`) |

Unique `{ message, user }`: one reaction per person per message; reacting again replaces it.
