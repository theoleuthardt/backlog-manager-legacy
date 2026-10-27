# Backlog Manager — legacy client

The first client of [Backlog Manager](https://github.com/theoleuthardt/backlog-manager),
a manager for your video game backlog: a React single-page app built with Vite
and React Router, shipped as a [Tauri](https://v2.tauri.app/) desktop app for
macOS, Windows and Linux and as a web image.

> [!IMPORTANT]
> **This client is retired and archived.** It was replaced by the Flutter
> desktop app in [`theoleuthardt/backlog-manager`](https://github.com/theoleuthardt/backlog-manager),
> whose first full release is
> [0.9.13](https://github.com/theoleuthardt/backlog-manager/releases/tag/v0.9.13).
> Nothing here is developed further, and no new releases are published.
>
> **Switching over:** install the new app from the
> [latest release](https://github.com/theoleuthardt/backlog-manager/releases/latest)
> and sign in with the same account. Your backlog lives on the server, so
> nothing has to be exported or imported.

## What it does

- Track games with metadata from IGDB and HowLongToBeat (cover, genres,
  release date, time to beat, trailer).
- Organize them by status or into your own categories with drag & drop,
  search, filter, sort and bulk-edit them.
- Connect a Steam account to import the library and wishlist and to sync
  playtimes and achievements.
- Share one backlog with a second person in a shared space.
- Import and export CSV files, and keep versioned backups of the backlog.
- Sign in with optional TOTP two-factor authentication; pick or create a
  colour theme.
- Desktop builds update themselves through the Tauri updater.

The backend behind all of this is the Litestar service in
[`backlog-manager/backend`](https://github.com/theoleuthardt/backlog-manager/tree/main/backend);
this repository only holds the client.

## History

The code lived in `frontend/` of `backlog-manager` from April 2025 until the
cutover to the Flutter app
([#287](https://github.com/theoleuthardt/backlog-manager/issues/287) there)
and was moved here with its full git history. It started on Next.js with tRPC
and NextAuth (create-t3-app) and was later rebuilt as a static Vite app that
talks to the backend over REST with a JWT, which is what made the Tauri build
possible.

## Stack

| Part        | Choice                                                                         |
| ----------- | ------------------------------------------------------------------------------ |
| UI          | React 19, React Router, Tailwind CSS 4, shadcn/ui (Radix)                      |
| Data        | TanStack Query, `openapi-fetch` client typed from the backend's OpenAPI schema |
| Drag & drop | dnd-kit                                                                        |
| Desktop     | Tauri 2 with the updater and opener plugins                                    |
| Web image   | nginx serving the static build (`Containerfile`)                               |
| Checks      | ESLint, TypeScript, Prettier, vitest                                           |

## Layout

```text
src/
  app/            pages (dashboard, account, login, setup, space, steam, CSV, themes),
                  contexts and components (_components/)
  components/ui/  shadcn/ui primitives
  hooks/          React Query hooks around the API modules
  lib/            sorting, filtering, grouping, themes (with vitest tests)
  lib/api/        typed REST client, one module per backend domain
src-tauri/        Tauri app (Rust), icons, capabilities, updater key
scripts/          release version and updater manifest scripts
docs/             TAURI.md (desktop app, updater, signing), DASHBOARD_UX.md
```

## Building

Needs Node 22 (`.nvmrc`) and, for the desktop app, Rust and the
[Tauri prerequisites](https://v2.tauri.app/start/prerequisites/).

```bash
cp .env.example .env    # NEXT_PUBLIC_API_URL: the backend to talk to
npm ci
npm run dev             # Vite dev server on port 3000
npm run check           # ESLint + TypeScript
npm test                # vitest
npm run build           # static build in dist/
npm run tauri:build     # desktop installers in src-tauri/target/release/bundle/
```

## Workflows

- **CI** (`ci.yml`): audit, lint, typecheck, format check and vitest, and the
  tests of the release scripts, on pull requests and pushes to `main`.
- **Image** (`image.yml`): builds the web image on pull requests. It is not
  published anymore.
- **Tauri Build** (`tauri-build.yml`, manual): the desktop apps for all three
  platforms as workflow artifacts. Needs the secrets
  `TAURI_SIGNING_PRIVATE_KEY` and `TAURI_SIGNING_PRIVATE_KEY_PASSWORD` and the
  variable `NEXT_PUBLIC_API_URL`.
- **Release Apps** (`release-apps.yml`, manual): a GitHub Release with the
  installers and `latest.json`. Installed apps look for updates in
  `backlog-manager` (`src-tauri/tauri.conf.json`), so such a release does not
  reach them.

More on the desktop app, its updater and the signing key in
[`docs/TAURI.md`](docs/TAURI.md); the dashboard design decisions are in
[`docs/DASHBOARD_UX.md`](docs/DASHBOARD_UX.md).
