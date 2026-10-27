# Backlog Manager (legacy client)

The original Backlog Manager client: a React single-page app built with Vite
and React Router, shipped as a Tauri desktop app for macOS, Windows and Linux
and as a web image.

> **Retired.** It was replaced by the Flutter app in
> [`theoleuthardt/backlog-manager`](https://github.com/theoleuthardt/backlog-manager),
> whose first full release is
> [0.9.13](https://github.com/theoleuthardt/backlog-manager/releases/tag/v0.9.13).
> This repository keeps the old client available and buildable; it is not
> developed further. Coming from this app, install the new one and sign in with
> the same account: the backlog lives on the server.

The code lived in `frontend/` of `backlog-manager` until the cutover (#287 there)
and moved here with its full git history.

## Building

Needs Node 22 (`.nvmrc`) and, for the desktop app, Rust and the
[Tauri prerequisites](https://v2.tauri.app/start/prerequisites/).

```bash
cp .env.example .env    # NEXT_PUBLIC_API_URL: the backend to talk to
npm ci
npm run dev             # Vite dev server
npm run check           # ESLint + TypeScript
npm test                # vitest
npm run tauri:build     # desktop installers in src-tauri/target/release/bundle/
```

The backend is the Litestar service in `backlog-manager` (`backend/`).

## Workflows

- **CI** (`ci.yml`): audit, lint, typecheck, format check and vitest, and the
  tests of the release scripts, on pull requests and pushes to `main`.
- **Image** (`image.yml`): builds the web image (`Containerfile`, served by
  nginx) on pull requests. It is not published anymore.
- **Tauri Build** (`tauri-build.yml`, manual): the desktop apps for all three
  platforms as workflow artifacts. Needs the secrets
  `TAURI_SIGNING_PRIVATE_KEY` and `TAURI_SIGNING_PRIVATE_KEY_PASSWORD` and the
  variable `NEXT_PUBLIC_API_URL`.
- **Release Apps** (`release-apps.yml`, manual): a GitHub Release with the
  installers and `latest.json`. Installed apps look for updates in
  `backlog-manager` (`src-tauri/tauri.conf.json`), so such a release does not
  reach them.

Details on the desktop app, its updater and the signing key:
[`docs/TAURI.md`](docs/TAURI.md); the dashboard design decisions:
[`docs/DASHBOARD_UX.md`](docs/DASHBOARD_UX.md).
