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
cp .env.example .env    # set JWT_ACCESS_SECRET and JWT_REFRESH_SECRET (64 random bytes each)
npm run dev:memory      # no MongoDB needed; or set MONGO_URI and run: npm run seed && npm run dev
```

Check: `curl http://localhost:5000/health` → `{"success":true,"data":{"status":"ok"}}`. Seed credentials: `.seed-credentials.local.json`.

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

Use `http://localhost:5000` for the iOS simulator and your computer's LAN IP for a physical device. On Windows, `android/gradle.properties` sets `kotlin.incremental=false`, because Kotlin incremental caches fail when the project and the Pub cache are on different drives.

## Verification commands

| Repository | Commands |
| --- | --- |
| Server | `npm run typecheck`, `npm test`, `npm run build` |
| Client | `npm run typecheck`, `npm run lint`, `npm run build` |
| Mobile | `flutter analyze`, `flutter test`, `flutter build apk --debug` |
