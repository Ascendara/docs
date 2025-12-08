# Developer Overview

Welcome to Ascendara's developer documentation. This guide covers the application architecture, codebase structure, and everything you need to contribute or build integrations.

## Architecture Overview

Ascendara is built on a modern Electron + React stack with a modular architecture:

```
┌─────────────────────────────────────────────────────────────────┐
│                        Renderer Process                          │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │                    React Frontend                        │    │
│  │  • Vite dev server    • TailwindCSS    • Framer Motion  │    │
│  └─────────────────────────────────────────────────────────┘    │
│                              │                                   │
│                         IPC Bridge                               │
│                        (preload.js)                              │
└──────────────────────────────┼──────────────────────────────────┘
                               │
┌──────────────────────────────┼──────────────────────────────────┐
│                        Main Process                              │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │                   electron/app.js                        │    │
│  │              (Entry point & orchestrator)                │    │
│  └─────────────────────────────────────────────────────────┘    │
│                              │                                   │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │                 electron/modules/                        │    │
│  │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐   │    │
│  │  │ config   │ │ settings │ │ downloads│ │  games   │   │    │
│  │  └──────────┘ └──────────┘ └──────────┘ └──────────┘   │    │
│  │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐   │    │
│  │  │ updates  │ │ themes   │ │ system   │ │ window   │   │    │
│  │  └──────────┘ └──────────┘ └──────────┘ └──────────┘   │    │
│  │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐   │    │
│  │  │ ludusavi │ │ steamcmd │ │ protocol │ │discord-rpc│  │    │
│  │  └──────────┘ └──────────┘ └──────────┘ └──────────┘   │    │
│  └─────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────┘
                               │
┌──────────────────────────────┼──────────────────────────────────┐
│                      Binary Tools (Python)                       │
│  ┌────────────────┐ ┌────────────────┐ ┌────────────────┐       │
│  │  Downloader    │ │  GameHandler   │ │ CrashReporter  │       │
│  └────────────────┘ └────────────────┘ └────────────────┘       │
│  ┌────────────────┐ ┌────────────────┐ ┌────────────────┐       │
│  │ GofileHelper   │ │ TorrentHandler │ │  Translator    │       │
│  └────────────────┘ └────────────────┘ └────────────────┘       │
└─────────────────────────────────────────────────────────────────┘
```

## Core Components

### Main Process (`electron/`)
The Electron main process handles system-level operations:

| Module | Purpose |
|--------|---------|
| `app.js` | Entry point, window creation, app lifecycle |
| `preload.js` | Secure IPC bridge between main and renderer |
| `modules/config.js` | App configuration, API keys, constants |
| `modules/settings.js` | User settings management |
| `modules/downloads.js` | Download orchestration and progress |
| `modules/games.js` | Game library, launching, shortcuts |
| `modules/updates.js` | Auto-update system |
| `modules/window.js` | Window management, fullscreen, tray |
| `modules/discord-rpc.js` | Discord Rich Presence integration |
| `modules/ludusavi.js` | Game save backup/restore |
| `modules/steamcmd.js` | Steam Workshop downloads |
| `modules/system.js` | System info, dependencies, drive space |
| `modules/themes.js` | Custom theme import/export |
| `modules/translations.js` | Language file management |
| `modules/protocol.js` | `ascendara://` URL handling |

### Renderer Process (`src/`)
The React frontend with modern UI components:

| Directory | Purpose |
|-----------|---------|
| `src/pages/` | Main application views |
| `src/components/` | Reusable UI components |
| `src/context/` | React context providers |
| `src/hooks/` | Custom React hooks |
| `src/lib/` | Utility functions |

### Binary Tools (`binaries/`)
Python executables for heavy operations:

| Tool | Purpose |
|------|---------|
| `AscendaraDownloader` | Multi-threaded download manager |
| `AscendaraGameHandler` | Game process management |
| `AscendaraGofileHelper` | GoFile.io download handling |
| `AscendaraTorrentHandler` | qBittorrent integration |
| `AscendaraCrashReporter` | Error reporting and diagnostics |
| `AscendaraLanguageTranslation` | AI-powered translations |
| `AscendaraNotificationHelper` | System notifications |
| `AscendaraAchievementWatcher` | Achievement tracking |

## IPC Communication

The renderer communicates with the main process via IPC channels exposed through `preload.js`:

```javascript
// Renderer (React)
const settings = await window.electron.getSettings();
await window.electron.downloadFile(link, game, online, dlc, ...);

// Main Process (modules)
ipcMain.handle("get-settings", () => settingsManager.getSettings());
ipcMain.handle("download-file", async (event, link, game, ...) => { ... });
```

See the [IPC Reference](/docs/developer/ipc-reference) for all available channels.

## Data Flow

### Download Flow
```
User clicks download
        │
        ▼
┌───────────────────┐
│  Renderer: Start  │
│  download request │
└─────────┬─────────┘
          │ IPC: download-file
          ▼
┌───────────────────┐
│  Main: Spawn      │
│  AscendaraDownloader│
└─────────┬─────────┘
          │ Child process
          ▼
┌───────────────────┐
│  Python: Download │──► Progress updates via JSON file
│  & Extract        │
└─────────┬─────────┘
          │
          ▼
┌───────────────────┐
│  Main: Monitor    │──► IPC: download-progress events
│  progress file    │
└─────────┬─────────┘
          │
          ▼
┌───────────────────┐
│  Renderer: Update │
│  UI with progress │
└───────────────────┘
```

### Game Launch Flow
```
User clicks play
        │
        ▼
┌───────────────────┐
│  Renderer: Launch │
│  game request     │
└─────────┬─────────┘
          │ IPC: play-game
          ▼
┌───────────────────┐
│  Main: Validate   │
│  executable       │
└─────────┬─────────┘
          │
          ▼
┌───────────────────┐
│  Main: Spawn game │──► Update Discord RPC
│  process          │──► Hide window (optional)
└─────────┬─────────┘
          │
          ▼
┌───────────────────┐
│  Main: Monitor    │──► Backup saves on close (optional)
│  game process     │──► Restore window on exit
└───────────────────┘
```

## Quick Links

| Topic | Description |
|-------|-------------|
| [Build from Source](/docs/developer/build-from-source) | Set up your development environment |
| [Project Structure](/docs/developer/project-structure) | Detailed codebase walkthrough |
| [IPC Reference](/docs/developer/ipc-reference) | All IPC channels documented |
| [Tech Stack](/docs/developer/tech-stack) | Technologies and dependencies |
| [Creating Tools](/docs/developer/creating-the-tools) | Building Python binaries |
| [Contributing](/docs/developer/contributing) | How to contribute to Ascendara |

## Development Mode Limitations

When running from source, certain features are restricted:
- **Analytics**: Disabled to prevent data pollution
- **Reporting**: Feature/game/download reporting disabled
- **Game Execution**: Games can only run from built versions

These restrictions protect Ascendara's infrastructure. See [Build from Source](/docs/developer/build-from-source) for details.

## Getting Help

- **Discord**: [ascendara.app/discord](https://ascendara.app/discord)
- **GitHub Issues**: [github.com/ascendara/ascendara/issues](https://github.com/ascendara/ascendara/issues)
- **API Docs**: [API Reference](/docs/api-reference/overview)