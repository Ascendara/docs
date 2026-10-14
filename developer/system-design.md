# System Design

A technical look at how Ascendara's processes, data, and services fit together.

## Process Architecture

```
┌────────────────────────────────────────────────────────────────┐
│                         Main Process                           │
│  electron/app.js — window, tray, local server, protocol        │
│  electron/modules/ — settings, downloads, games, updates, …    │
│                                                                │
│                    Child processes (spawn)                     │
│  Downloader · TorrentHandler · GameHandler · NotificationHelper│
│  AchievementWatcher · CrashReporter · Ludusavi · SteamCMD      │
└───────────────────────────────┬────────────────────────────────┘
                                │ contextBridge (preload.js)
┌───────────────────────────────┴────────────────────────────────┐
│                       Renderer Process                         │
│  React app served from http://localhost:46859 (packaged)       │
│  or http://localhost:5173 (Vite, development)                  │
└────────────────────────────────────────────────────────────────┘
```

The renderer runs with context isolation and no Node integration. It can only reach the main process through the API exposed in `preload.js`.

## Module System

`electron/modules/index.js` loads the main-process modules in two groups:

- **Critical modules**, loaded at startup: config, logger, encryption, security, recovery, settings, window, Discord RPC, protocol, tools, updates, downloads, games, system, and the miscellaneous IPC handlers.
- **Lazy modules**, loaded the first time they're used: SteamCMD, local refresh, Ludusavi, translations, themes, QR codes, and the UMU database.

Before any feature module registers a handler, `index.js` installs the IPC security guard (see [Security](#security)).

## IPC Communication Pattern

### Request-Response (invoke/handle)
```
Renderer                         Main Process
   │  window.electron.getSettings()   │
   │  → invoke("get-settings")        │
   │ ───────────────────────────────► │  security guard checks the sender
   │                                  │  settingsManager.getSettings()
   │ ◄─────────────────────────────── │
   │        { settings object }       │
```

### Events (send/subscribe)
```
Main Process                     Renderer
   │  webContents.send(              │
   │    "game-closed", { game }      │
   │  )                              │
   │ ──────────────────────────────► │  window.electron.onGameClosed(callback)
```

Subscription methods return a function that removes the listener. Listeners never receive the raw Electron event object.

## Download System

```
┌──────────────────────────────────────────────────────────────┐
│ downloads.js                                                 │
│  • Validates the link and the download directory             │
│  • Writes {game}.ascendara.json                              │
│  • Spawns AscendaraDownloader (HTTP, Gofile)                 │
│    or AscendaraTorrentHandler (magnet links)                 │
│  • Handles stop, kill, resume, retry, and retry extraction   │
└─────────────────────────────┬────────────────────────────────┘
                              │ spawn with positional arguments
┌─────────────────────────────┴────────────────────────────────┐
│ AscendaraDownloader                                          │
│  • Resumable multi-connection transfers                      │
│  • Extraction in a worker process (zip, UnRAR, 7-Zip)        │
│  • Staged install with rollback, then verification           │
│  • Writes progress to downloadingData                        │
└──────────────────────────────────────────────────────────────┘
```

The renderer polls `getDownloads()` once per second. The main process reads each game's `{game}.ascendara.json` and returns its `downloadingData`. When the download finishes, the helper removes `downloadingData`.

### Download States
```
DOWNLOADING ──► EXTRACTING ──► VERIFYING ──► COMPLETE
     │              │              │
     └──────────────┴──────────────┴──► ERROR (error, message, or verifyError)
```

`downloadingData` can also contain `updating` (installing over an existing game), `stopped` (a stop request), `waiting` (torrent waiting for peers), and `pendingManualInstall` (an installer must be run by the user). See the [Downloader](/docs/binary-tool/downloader#state-and-files).

## Game Management

### Game Data Structure

Each downloaded game has a `{game}.ascendara.json` in its folder. Custom and imported games are stored in `games.json` in the download directory.

```json
{
  "game": "GameName",
  "online": false,
  "dlc": false,
  "isVr": false,
  "version": "1.0.0",
  "size": "20 GB",
  "imgID": "abc123",
  "gameID": "AbCdEf",
  "executable": "C:/Games/GameName/game.exe",
  "executables": ["C:/Games/GameName/game.exe"],
  "isRunning": false,
  "launchCount": 3,
  "playTime": 5400,
  "backups": true,
  "launchCommands": "-windowed"
}
```

`playTime` is stored in seconds.

### Game Launch Sequence
```
play-game(game, isCustom, backupOnClose, launchWithAdmin,
          specificExecutable, launchWithTrainer, installation)
    │
    ├─► Resolve the executable (or the one the user picked)
    ├─► Linux: resolve the runner (UMU / Proton / Wine) and UMU ID
    ├─► Spawn AscendaraGameHandler with arguments
    ├─► Update Discord Rich Presence
    ├─► Hide the Ascendara window (if Hide on Game Launch is on)
    │
    └─► GameHandler exits:
            ├─► game-closed event to the renderer
            └─► Show the Ascendara window
```

The Game Handler itself increments `launchCount`, sets `isRunning`, adds playtime every 3 minutes (and the remainder when the game closes), launches the trainer, and runs the Ludusavi backup after the game closes. See the [Game Handler](/docs/binary-tool/game-handler).

## Settings System

### SettingsManager

```javascript
class SettingsManager {
  constructor() {
    this.filePath = path.join(app.getPath("userData"), "ascendarasettings.json");
    this.sensitiveKeys = ["twitchSecret", "twitchClientId", "torboxApiKey"];
    // Creates the file with defaults if it doesn't exist,
    // loads it, and encrypts any plaintext sensitive keys.
  }

  getSettings() { /* returns settings with sensitive keys decrypted */ }
  saveSettings(newSettings) { /* merges, encrypts, writes, sends "settings-updated" */ }
  updateSetting(key, value) { /* updates one key */ }
}
```

Sensitive keys are encrypted with AES-256-CBC. The key is derived from a SHA-256 hash of the machine ID, so the encrypted values only work on the same computer.

### Settings File Location
| Platform | Path |
|----------|------|
| Windows | `%APPDATA%\ascendara\ascendarasettings.json` |
| macOS | `~/Library/Application Support/ascendara/ascendarasettings.json` |
| Linux | `~/.config/ascendara/ascendarasettings.json` |

The helper tools read the same file to get settings such as the download speed limit, thread count, Ludusavi options, and notification theme.

### App State

`timestamp.ascendara.json` in the user's home folder stores app state that isn't a setting, such as whether the index has been set up (`hasIndexBefore`), installed sidecars (`installedTools`), the extra-language version, and whether the Achievement Watcher is running.

### Settings Recovery Points

`recovery.js` stores snapshots of the settings file in `<userData>/recovery-points`. Snapshots are created manually from the [System Center](/docs/features/system-center) and automatically before an update is installed and before a rollback.

## Update System

### Update Flow
```
App start
    │
    ├─► Check broken versions (GET /app/brokenversions)
    │
    ├─► Get the latest version
    │       Live branch:    GET https://api.ascendara.app/  → appVer
    │       Other branches: GET /branch-versions
    │
    └─► Newer version?
            ├─► No: continue
            └─► Yes:
                  ├─► Auto Update on → download in the background
                  │       Windows/Linux: lfs.ascendara.app/download?update
                  │                      (or ?branch=<branch>)
                  │       macOS:         the mac-<arch>.zip from the GitHub release
                  │   → "update-ready" → user clicks Install & Restart
                  │
                  └─► Auto Update off → "update-available" notification
```

Installing an update replaces the app and restarts it. On Windows the NSIS installer runs; on Linux the running AppImage is replaced; on macOS the `.app` is replaced after Ascendara quits. macOS testing branches don't update automatically.

### Release Branches

| Branch | Description |
| --- | --- |
| `live` | Stable releases |
| `public-testing` | Nearly-ready features |
| `experimental` | Early, possibly unstable builds (Ascend subscribers) |

Users switch branches in **Settings** → **App Branch**, which downloads and runs that branch's installer. Branch switching isn't available on macOS. The app's branch, version, and testing version are set in `electron/modules/config.js` by the build process and must not be changed by hand.

## Game Index

The renderer reads games from `ascendara_games.json` in the local index folder. `community-index.js` keeps it in sync with `https://api.ascendara.app/json/v2/games`, using the response's ETag to skip unchanged downloads, and downloads covers on demand from `r2.ascendara.app`. When the API can't reach the game source, `index-relay.js` can fetch the source pages from the user's connection on the API's behalf. See [Your Game Index](/docs/features/refreshing-index).

## Protocol Handler

Ascendara registers the `ascendara://` scheme. Only one instance runs at a time; if Ascendara is already running, the URL is passed to the existing instance.

| URL | Purpose |
| --- | --- |
| `ascendara://download?payload=<json>` | A download sent by the browser extension. The JSON contains `url`, and optionally `filename`, `title`, and `pageUrl`. Older extension versions send `ascendara://<encoded url>`. |
| `ascendara://game?<gameID>` | Opens a game's page |
| `ascendara://checkout-success?session_id=cs_…` | Completes an Ascend checkout |
| `ascendara://checkout-canceled` | A canceled Ascend checkout |
| `ascendara://show-window` | Shows the Ascendara window |

Download URLs must be `http` or `https` and can't contain credentials.

## Sidecars

Optional tools are installed on demand ("sidecars"). Their status is shown on the **Application Components** page.

| Sidecar | Source | Used for |
| --- | --- | --- |
| Language Translation (Windows) | `cdn.ascendara.app/files/AscendaraLanguageTranslation.exe` | Extra languages |
| Torrent Handler (Windows) | `cdn.ascendara.app/files/AscendaraTorrentHandler.exe` | Torrent downloads |
| Ludusavi | `cdn.ascendara.app/files/ludusavi.exe` (or `linux/`, `mac/`) | Save backups |
| SteamCMD | Installed by `steamcmd.js` | Workshop Downloader |

On Windows, installed sidecars are recorded in `installedTools` in the timestamp file and re-downloaded if they go missing.

## Security

- **IPC guard**: `security.js` wraps every `ipcMain.handle`. A call is rejected unless it comes from the main frame of Ascendara's own window, loaded from `localhost:46859` (packaged) or `localhost:5173` (development).
- **Context isolation**: The renderer has no Node.js access. It can only use the methods exposed in `preload.js`.
- **Restricted service requests**: `window.electron.request()` (`requestAscendaraService`) only allows `GET` and `HEAD` requests to the root of Ascendara's service hosts (`api`, `cdn`, `lfs`, `r2`, and `monitor` on `ascendara.app`), for status checks. Other main-process requests go through dedicated IPC methods.
- **External links**: Only `http` and `https` URLs without embedded credentials are opened in the browser.
- **Signed API requests**: Official builds sign requests to protected API endpoints with rotating HMAC headers and include a build signature. Source builds don't have the secret.

## Error Handling

```
Helper tool error
    │
    ├─► Downloader/TorrentHandler: write error + message to downloadingData
    │       → shown on the download card
    │
    └─► Unhandled exception: register the Crash Reporter to open on exit
            AscendaraCrashReporter <tool-id> <code> <message>
```

See [Error Codes](/docs/troubleshooting/error-codes) and the [Crash Reporter](/docs/binary-tool/crash-reporter).
