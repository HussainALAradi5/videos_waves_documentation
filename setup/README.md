# Setup

Repositories sit side by side in the `videos_waves` folder:

```text
videos_waves/
  videos_waves_server/
  videos_waves_client/
  videos_waves_mobile/
  videos_waves_documentation/
```

## 1. Server

```bash
cd videos_waves_server
npm install
cp .env.example .env    # set the JWT secrets and MEDIA_LOCAL_SECRET (64 random bytes each)
npm run dev:memory      # in-memory MongoDB replica set, seeded; no database needed
```

With a real database, `MONGO_URI` must point to a **replica set** (MongoDB Atlas is one). Then run `npm run migrate`, `npm run seed`, and `npm run dev`.

Check: `curl http://localhost:5000/health` → `{"success":true,"data":{"status":"ok"}}`. Seed credentials are in `.seed-credentials.local.json`.

**Media:** `MEDIA_PROVIDER=local` stores uploads on disk for development. For a live demo, create a free ImageKit account and set `MEDIA_PROVIDER=imagekit` plus the three `IMAGEKIT_*` keys (see [integrations](../integrations/README.md)). Schedule `npm run media:cleanup` (for example daily).

## 2. Web client

```bash
cd videos_waves_client
npm install
cp .env.example .env.local   # NEXT_PUBLIC_API_BASE_URL=http://localhost:5000
npm run dev                  # http://localhost:3000
```

The server's `CORS_ORIGINS` must include `http://localhost:3000`.

## 3. Mobile

```bash
cd videos_waves_mobile
flutter pub get
flutter run --dart-define=API_BASE_URL=http://10.0.2.2:5000
```

Use `http://localhost:5000` for the iOS simulator or a web build, and your computer's LAN IP for a physical device. A web build served on another port needs that origin in `CORS_ORIGINS` (for example `http://localhost:8082`). On Windows, `android/gradle.properties` sets `kotlin.incremental=false`, because Kotlin incremental caches fail when the project and the Pub cache are on different drives.

## Verification commands

| Repository | Commands |
| --- | --- |
| Server | `npm run lint`, `npm run typecheck`, `npm test`, `npm run build` |
| Client | `npm run lint`, `npm run typecheck`, `npm test`, `npm run build` |
| Mobile | `flutter analyze`, `flutter test`, `flutter build apk --debug`, `flutter build web --dart-define=API_BASE_URL=http://localhost:5000` |
