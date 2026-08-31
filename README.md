# Care Suite Ultima — web and desktop delivery

This project converts the supplied single-device demo into a shared web service with PostgreSQL storage and desktop packages for Windows, macOS, and Linux.

## 1. Prepare the existing interface

Install Node.js 22 and run the following in this folder. The command copies the supplied HTML into the web application and injects state synchronisation.

```powershell
npm install
$env:SOURCE_HTML = 'C:\Users\FFS\Downloads\care_suite_ultima (1).html'
npm run prepare-client
Copy-Item .env.example .env
```

Set unique values for `POSTGRES_PASSWORD`, `JWT_SECRET`, and `BOOTSTRAP_ADMIN_PASSWORD` in `.env`. Do not retain the demo administrator credential printed in the original page.

## 2. Run the web system

```powershell
docker compose up --build
```

Open `http://localhost:3000`. The health check is `http://localhost:3000/api/health`.

## 3. Sign in

`prepare-client` replaces the prototype’s local demo-login logic with `POST /api/auth/login` and synchronises state only after an authenticated session is established. Sign in with `BOOTSTRAP_ADMIN_EMAIL` and `BOOTSTRAP_ADMIN_PASSWORD` from `.env`.

## 4. Build desktop installers

With the web service reachable, set `CARESUITE_URL` to its HTTPS address and run:

```powershell
npm run dist
```

Artifacts are written to `dist/`: NSIS and portable Windows installers, macOS DMG, Linux AppImage, and Debian package. Build macOS artifacts on a macOS signing machine for a distributable signed package.

## 5. Mobile client extension

The `mobile` folder is an Expo client for Android and iOS. It uses the same `/api/auth/login` and `/api/state` endpoints as the web and desktop clients; credentials are stored in the device secure store, not in the app database.

```powershell
cd mobile
npm install
$env:EXPO_PUBLIC_CARESUITE_API_URL = 'https://care.example.com'
npm run android   # or: npm run ios
```

Use an HTTPS URL reachable from the phone. `http://10.0.2.2:3000` is only the Android-emulator development default. The initial mobile scope is read-only patient registry and bookings; retain the web client as the clinical-record editing workflow until per-record permissions, audit logs, and offline-conflict handling are added.

## Release safeguards

This is an implementation baseline, not a medical compliance certification. Put the public service behind HTTPS, make database backups, restrict database network exposure, manage secrets outside source control, and conduct privacy/security review before storing real patient data.
