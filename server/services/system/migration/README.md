# Migration service

### `runPendingMigrations(): Promise<string[]>`
Runs every migration in `src/migrations/index.ts` that has no `Migration` row, in order, records each one, and returns the ids applied. `npm run migrate` calls it. See [globals → migrations](../../../globals/README.md#migrations).
