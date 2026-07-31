<p align="center">
  <img src="Image/DraconDex-SymbolColor.png" alt="DraconDex" width="120">
</p>

<h1 align="center">DraconDex</h1>
<p align="center"><b>Novel / World-building data manager</b> — Author: LDKTC</p>

---

DraconDex is a desktop app (with an Android companion) for writers who need to
keep track of everything that goes into a novel, game, or any long-running
story: characters, items, clans, timelines, relationships, maps, and more —
all in one place instead of scattered across notes apps.

It grew out of a personal need: 100+ stories worth of world-building notes
that no existing note-taking app could organize well, either because they
were too rigid (fixed templates) or too limited behind a paywall. DraconDex
lets you define your **own** category templates for anything — characters,
monsters, items, artifacts, clans — and connect them with timelines, maps,
and relationship graphs.

> **Note:** As of v1.2.0 the app UI is Thai-only. A future release
> (see [Update plan](#update-plan--roadmap)) will add English, Japanese, and
> Korean UI languages. The current codebase (v2.x) already ships an 18-locale
> i18n system (`src/renderer/i18n.js`), with Thai as the default.

## Table of contents

- [Modules](#modules)
- [Tech stack](#tech-stack)
- [Getting started](#getting-started)
  - [Desktop app (Electron)](#desktop-app-electron)
  - [Android app (Flutter)](#android-app-flutter)
- [Building the desktop app](#building-the-desktop-app)
- [Data storage](#data-storage)
- [Project structure](#project-structure)
- [Documentation](#documentation)
- [Update plan / roadmap](#update-plan--roadmap)
- [License](#license)

## Modules

DraconDex is organized into six main modules that share a single SQLite
database:

| Module | Purpose |
|---|---|
| **Director** | The core novel database: projects & folders, custom categories with your own field templates (text / number / text area), objects (characters, items, etc.), and their attributes. Includes list and table views. |
| **Navigator** | "World" management — links multiple novels (from Director) into one shared world/multiverse, world characters, and world map timelines. |
| **Hero** | Game design built on top of your novel data — game characters (with leveled attributes), item/skill collections, and a dialogue/storyline whiteboard. |
| **Writer** | Manuscript writing — projects → series → books → chapters, with an autosaving editor, wiki-style word links back to your novel objects, and chat-style notes. |
| **Sage** | Read-only stats & analysis — data size, object counts, and a cross-module link graph. |
| **Artisan** | Scaffolds a full project (e.g. "standard novel": characters/places/items + a main timeline) from a template in one step. |

Cross-cutting project-level tools available inside Director projects:

- **Timeline** — multiple timelines per project, events with custom start/end
  dates (supports non-standard calendars, e.g. sci-fi "day 44, month 15").
- **Relation** — define your own relation types and connect Object↔Object,
  Object↔Event, or Event↔Event, visualized as an interactive force-graph.
- **Map** — draw maps and polygon areas for locations in your story.
- **Tags** — global tags searchable across projects, objects, and events.
- **Search** — a single search box across projects, objects, and tags.
- **Import / Export DB** — back up or move your entire database as one file.

## Tech stack

- **Desktop:** [Electron](https://www.electronjs.org/) + vanilla JS renderer
  (no framework), [`node-sqlite3-wasm`](https://www.npmjs.com/package/node-sqlite3-wasm)
  for storage, packaged with `electron-builder`.
- **Mobile:** [Flutter](https://flutter.dev/) app in [`flutter_app/`](flutter_app),
  reading/writing the same SQLite schema, built independently from the
  Electron app.

## Getting started

### Desktop app (Electron)

Requirements: [Node.js](https://nodejs.org/) and npm.

```bash
npm install     # installs dependencies (also fetches Electron via postinstall)
npm start       # launches the app
```

### Android app (Flutter)

Requirements: Flutter SDK 3.44.4+, Android Studio with Android SDK 24+, and
either a phone with USB debugging enabled or an Android emulator.

```bash
cd flutter_app
flutter pub get
flutter doctor   # make sure the Android toolchain has no red X
flutter run
```

You can also build the APK without a local Flutter setup via the
**Build Flutter APK** GitHub Actions workflow (Actions tab → run workflow →
download the `release-apks` artifact).

To move your data from the PC app to the phone: export/copy the
`novel-manager.db` file from your PC user data folder, then use
**Settings → Import Database** in the Android app.

For the full step-by-step guide (including manual APK builds and installing
on a device), see [`Install-Guide.txt`](Install-Guide.txt).

## Building the desktop app

```bash
npm run build:portable    # portable app folder (~200-220MB), DraconDexPortable/DraconDex-<version>/
npm run build:installer   # Windows installer (.exe) with desktop/start-menu shortcuts
npm run build:exe         # legacy single-file portable .exe
```

A portable build can be copied to a flash drive and run on any Windows PC
with no installation — its data folder (`novel-manager-data/`) travels with
it.

## Data storage

| Mode | Data location |
|---|---|
| Dev (`npm start`) | `tmp-user-data/` in the repo (override with env `DRACONDEX_DATA_DIR`) |
| Portable build | `novel-manager-data/` next to the executable |
| Installed via installer | `%APPDATA%/DraconDex/novel-manager-data/` |

Each data folder is single-instance locked — you can't open two windows on
the same data, but a separate data folder can run alongside a dev instance.

## Project structure

```
DraconDex/
├─ main.js            # Electron main process + all IPC handlers
├─ preload.js         # window.api bridge (contextBridge)
├─ database.js        # aggregates src/db/* exports
├─ index.html         # empty shell — UI is built by JS at runtime
├─ style.css           # app styles + all themes
├─ start.js           # npm start entry point
├─ ensure-electron.js # checks/repairs the Electron binary (postinstall)
├─ src/
│  ├─ db/             # database layer (main process), one file per module
│  └─ renderer/       # UI layer (renderer process), one file per module
├─ scripts/           # build helper scripts
├─ Image/             # app icons/logo
├─ flutter_app/        # Flutter (Android) port, shares the DB schema
└─ docs/              # developer documentation
```

## Documentation

For a deeper dive into how each module and file works, see:

- [`docs/SYSTEMS.md`](docs/SYSTEMS.md) — architecture and behavior of each module
- [`docs/FILES.md`](docs/FILES.md) — file-by-file breakdown

## Update plan / roadmap

All code is free to use through the 2.x.0 series. The 1.x.0 line focused on
the core novel manager and making it flexible to use; from 2.x onward the
plan is to expand into a full **World manager** — connecting multiple novel
projects that share the same world or multiverse into a single world
project.

## License

[MIT](LICENSE) © 2026 LDKTC
