# Developer Overview

Welcome to Ascendara's developer documentation. This guide covers the application architecture, codebase structure, and everything you need to contribute or build integrations.

## Architecture Overview

Ascendara is an Electron + React app. The React renderer talks to the Electron main process through a preload bridge, and the main process runs native helper tools for heavy or platform-specific work:

```
┌─────────────────────────────────────────────────────────────────┐
│                        Renderer Process                         │
│   React 19 · Vite · Tailwind CSS · shadcn/ui · Framer Motion    │
│   src/pages · src/components · src/services · src/context       │
└────────────────────────────────┬────────────────────────────────┘
                                 │ window.electron (contextBridge)
                       electron/preload.js
                                 │ ipcRenderer.invoke / events
┌────────────────────────────────┴────────────────────────────────┐
│                          Main Process                           │
│  electron/app.js — lifecycle, tray, local server, protocol      │
│  electron/modules/ — settings, downloads, games, updates,       │
│    local index, retro, proton, ludusavi, security, recovery …   │
└────────────────────────────────┬────────────────────────────────┘
                                 │ child processes
┌────────────────────────────────┴────────────────────────────────┐
│                         Helper Binaries                         │
│  Downloader · GameHandler · TorrentHandler · NotificationHelper │
│  LanguageTranslation (Python)  ·  CrashReporter (Rust)          │
│  AchievementWatcher (Node.js)  ·  Ludusavi, SteamCMD (external) │
└─────────────────────────────────────────────────────────────────┘
```

In packaged builds, the renderer is served from a local HTTP server at `http://localhost:46859`. In development it's served by Vite at `http://localhost:5173`. The local server also proxies a few third-party APIs (TorBox, KHInsider, FLiNG) for the renderer.

## Core Components

### Main Process (`electron/`)

| File | Purpose |
|--------|---------|
| `app.js` | Entry point: window creation, tray, local server, protocol registration, app lifecycle |
| `preload.js` | Exposes the `window.electron` API to the renderer |
| `modules/index.js` | Loads modules; installs the IPC security guard first; lazy-loads non-critical modules |
| `modules/config.js` | App version and branch, platform detection, paths, helper paths |
| `modules/settings.js` | `SettingsManager`: loads, saves, and encrypts settings |
| `modules/security.js` | Rejects IPC calls that don't come from Ascendara's own window |
| `modules/downloads.js` | Starts the downloader and torrent handler, tracks progress, retries, queue |
| `modules/games.js` | Library, game launching, executables, shortcuts, assets |
| `modules/local-refresh.js`, `community-index.js`, `index-relay.js` | Local game index sync, covers, and index relay |
| `modules/launcher-discovery.js`, `launcher-import.js` | Importing games from other launchers |
| `modules/retro/` | Ascendara Retro library, scanning, catalogue, launching, saves |
| `modules/proton.js`, `umu-database.js`, `wine-setup.js` | Linux and macOS compatibility layers |
| `modules/ludusavi.js` | Save backup and restore |
| `modules/updates.js`, `install-update.js` | App updates and branch switching |
| `modules/recovery.js` | Settings recovery points and version rollback |
| `modules/protocol.js` | `ascendara://` URL handling |
| `modules/discord-rpc.js` | Discord Rich Presence |
| `modules/system.js` | System info, drive space, dependencies, Defender exclusions |

See [Project Structure](/docs/developer/project-structure) for the full list.

### Renderer Process (`src/`)

| Directory | Purpose |
|-----------|---------|
| `src/pages/` | Pages, including Big Picture (`big-picture/`) and Ascend (`ascend/`) |
| `src/components/` | Reusable components and shadcn/ui primitives (`ui/`) |
| `src/services/` | Business logic and external APIs (Firebase, game info, downloads, TorBox, …) |
| `src/context/` | React context providers (auth, language, settings, theme, search, tour) |
| `src/hooks/` | Custom React hooks |
| `src/lib/` | Utility functions |
| `src/translations/` | Language files (`en.json` is the source) |

### Helper Binaries (`binaries/`)

| Tool | Language | Purpose |
|------|----------|---------|
| [AscendaraDownloader](/docs/binary-tool/downloader) | Python | Download, extract, verify, and install games (including Gofile) |
| [AscendaraGameHandler](/docs/binary-tool/game-handler) | Python | Launch games, track playtime, Discord RPC, backups on close |
| [AscendaraTorrentHandler](/docs/binary-tool/torrent-handler) | Python | Torrent downloads through qBittorrent |
| [AscendaraNotificationHelper](/docs/binary-tool/notification-helper) | Python (PyQt6) | Desktop and achievement notifications |
| [AscendaraLanguageTranslation](/docs/binary-tool/language-translator) | Python | Translates the UI into extra languages |
| [AscendaraCrashReporter](/docs/binary-tool/crash-reporter) | Rust (FLTK) | Error reporting window |
| [AscendaraAchievementWatcher](/docs/binary-tool/achievement-watcher) | Node.js | Watches achievement files |
| [AscendaraLocalRefresh](/docs/binary-tool/local-refresh) | Python | Game index scraper (not shipped with the app) |

## IPC Communication

The renderer calls the main process through the API exposed by `preload.js`:

```javascript
// Renderer (React)
const settings = await window.electron.getSettings();
await window.electron.downloadFile(link, game, online, dlc, isVr, updateFlow, version, imgID, size, additionalDirIndex, gameID);

// Main process (electron/modules)
ipcMain.handle("get-settings", () => settingsManager.getSettings());
ipcMain.handle("download-file", async (event, link, game, ...) => { ... });
```

See the [IPC Reference](/docs/developer/ipc-reference) for the available methods and events.

## Data Flow

### Download Flow
```
User clicks download
        │
        ▼
Renderer: window.electron.downloadFile(...)
        │ IPC: download-file
        ▼
Main: create game folder + {game}.ascendara.json,
      spawn AscendaraDownloader (or TorrentHandler for magnets)
        │
        ▼
Helper: transfer → extract → verify → install,
        writing progress to downloadingData in {game}.ascendara.json
        │
        ▼
Renderer: Downloads page polls window.electron.getDownloads()
          every second; the main process reads downloadingData
          from each game's {game}.ascendara.json
```

### Game Launch Flow
```
User clicks Play
        │
        ▼
Renderer: window.electron.playGame(game, isCustom, backupOnClose, ...)
        │ IPC: play-game
        ▼
Main: resolve executable and launch commands;
      on Linux add Proton/UMU/Wine runner arguments;
      spawn AscendaraGameHandler; update Discord RPC; hide window
        │
        ▼
GameHandler: launch game, track playtime and launch count,
             launch trainer, run Ludusavi backup on exit
        │
        ▼
Main: game-closed event → show window, reset Discord RPC
```

## Development Mode Limitations

When running from source (`yarn start`):
- **Games** can't be launched.
- **Ascend** features are disabled unless you configure Firebase credentials.
- **Protected API endpoints** (such as reports, ratings, and authenticated requests) may reject requests, because source builds don't include the private request-signing secret.

See [Building from Source](/docs/developer/build-from-source#important-limitations) for details.

## Quick Links

| Topic | Description |
|-------|-------------|
| [Build from Source](/docs/developer/build-from-source) | Set up your development environment and build the app |
| [Project Structure](/docs/developer/project-structure) | Codebase walkthrough |
| [System Design](/docs/developer/system-design) | Settings, data files, updates, protocol, security |
| [IPC Reference](/docs/developer/ipc-reference) | The `window.electron` API |
| [Tech Stack](/docs/developer/tech-stack) | Technologies and dependencies |
| [Creating the Tools](/docs/developer/creating-the-tools) | Building the helper binaries |
| [Webview Development](/docs/developer/webview) | Ascendara Companion |
| [Contributing](/docs/getting-started/contributing) | How to contribute to Ascendara |

## Getting Help

- **Discord**: [ascendara.app/discord](https://ascendara.app/discord)
- **GitHub Issues**: [github.com/Ascendara/ascendara/issues](https://github.com/Ascendara/ascendara/issues)
- **API Docs**: [API Reference](/docs/api-reference/overview)
