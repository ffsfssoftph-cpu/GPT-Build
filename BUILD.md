# Release build guide

The package contains the production web app, API, desktop shell, mobile client, Docker deployment, dependency lock file, and a reproducible desktop build script.

## Windows

Run this from PowerShell on a Windows build machine with Node.js 22+:

```powershell
Set-Location <this-folder>
.\scripts\build-release.ps1 -Target windows
```

This emits NSIS setup and portable `.exe` artifacts to `dist`. Code-sign them with the organisation’s Authenticode certificate before distribution.

This release folder also contains a tested `dist/Care Suite Ultima 1.0.0-win-x64-portable.zip` bundle and its unpacked `dist/win-unpacked/Care Suite Ultima.exe`. Extract the ZIP and run the executable; configure `CARESUITE_URL` to point it at the deployed Care Suite web service.

## macOS and Linux

Build each target on its native platform:

```powershell
./scripts/build-release.ps1 -Target macos
./scripts/build-release.ps1 -Target linux
```

macOS distribution requires Apple signing and notarisation credentials. Linux packages are unsigned by design.

## Web API and database

Copy `.env.example` to `.env`, set unique secrets, then run `docker compose up --build`. Desktop and mobile apps connect to that HTTPS service through `CARESUITE_URL` and `EXPO_PUBLIC_CARESUITE_API_URL` respectively.
