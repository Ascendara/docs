# System Design

Technical deep-dive into Ascendara's architecture and data flows.

## Process Architecture

Ascendara uses Electron's multi-process architecture:

```
┌────────────────────────────────────────────────────────────────┐
│                         Main Process                           │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │                      electron/app.js                     │  │
│  │  • Window management    • IPC handling    • App lifecycle│  │
│  └──────────────────────────────────────────────────────────┘  │
│                              │                                 │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │                   electron/modules/                      │  │
│  │  • Settings     • Downloads    • Games      • Updates    │  │
│  │  • System       • Themes       • Discord    • Protocol   │  │
│  └──────────────────────────────────────────────────────────┘  │
│                              │                                 │
│                    Child Processes (spawn)                     │
│  ┌────────────┐ ┌────────────┐ ┌────────────┐ ┌────────────┐   │
│  │ Downloader │ │GameHandler │ │GofileHelper│ │ Ludusavi   │   │
│  └────────────┘ └────────────┘ └────────────┘ └────────────┘   │
└────────────────────────────────────────────────────────────────┘
                               │
                          preload.js
                        (contextBridge)
                               │
┌────────────────────────────────────────────────────────────────┐
│                       Renderer Process                         │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │                     React Application                    │  │
│  │  • Pages        • Components    • Context    • Hooks     │  │
│  └──────────────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────────────┘
```

## Module System

The main process is organized into focused modules:

### Module Initialization Flow
```
app.js (entry)
    │
    ├─► require("./modules")
    │       │
    │       ├─► config.js      (loads first - constants)
    │       ├─► logger.js      (logging setup)
    │       ├─► settings.js    (SettingsManager singleton)
    │       ├─► encryption.js  (crypto helpers)
    │       └─► ... other modules
    │
    ├─► Initialize logger
    ├─► Register all IPC handlers
    ├─► Create BrowserWindow
    └─► Handle app lifecycle
```

### Module Dependencies
```
config.js ◄─────────────────────────────────────┐
    │                                           │
    ▼                                           │
settings.js ◄───────────────────────────┐       │
    │                                   │       │
    ▼                                   │       │
┌─────────────────────────────────────────────────┐
│  downloads.js  │  games.js  │  updates.js │ ... │
│      │              │             │             │
│      └──────────────┴─────────────┴─────────────┤
│                 All use settings & config       │
└─────────────────────────────────────────────────┘
```

## IPC Communication Pattern

### Request-Response (invoke/handle)
```
Renderer                    Main Process
   │                             │
   │  invoke("get-settings")     │
   │ ──────────────────────────► │
   │                             │  settingsManager.getSettings()
   │                             │
   │  ◄───────────────────────── │
   │      { settings object }    │
```

### Event-Based (send/on)
```
Main Process                Renderer
   │                             │
   │  event.sender.send(         │
   │    "download-progress",     │
   │    { progress: 45 }         │
   │  )                          │
   │ ──────────────────────────► │
   │                             │  onDownloadProgress(callback)
```

## Download System

### Architecture
```
┌─────────────────────────────────────────────────────────────┐
│                     Download Manager                        │
│  ┌─────────────────────────────────────────────────────┐    │
│  │                  downloads.js                       │    │
│  │  • Spawns Python downloader                         │    │
│  │  • Monitors progress file                           │    │
│  │  • Broadcasts progress to renderer                  │    │
│  └─────────────────────────────────────────────────────┘    │
│                           │                                 │
│                     spawn process                           │
│                           ▼                                 │
│  ┌─────────────────────────────────────────────────────┐    │
│  │              AscendaraDownloader.py                 │    │
│  │  • Multi-threaded downloads (pySmartDL)             │    │
│  │  • Archive extraction (patool/rarfile)              │    │
│  │  • Progress JSON updates                            │    │
│  └─────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────┘
```

### Progress File Format
```json
{
  "game": "GameName",
  "downloadingData": {
    "downloading": true,
    "extracting": false,
    "progressCompleted": "1.2 GB",
    "progressDownloadSpeeds": "45.2 MB/s",
    "timeUntilComplete": "0:02:30"
  }
}
```

### Download States
```
IDLE ──► DOWNLOADING ──► EXTRACTING ──► VERIFYING ──► COMPLETE
                │              │             │
                └──────────────┴─────────────┴──► ERROR
```

## Game Management

### Game Data Structure
```json
{
  "game": "GameName",
  "online": false,
  "dlc": false,
  "version": "1.0.0",
  "executable": "C:/Games/GameName/game.exe",
  "isRunning": false,
  "imgID": "abc123",
  "backups": true,
  "rated": false,
  "launchCommands": {
    "pre": "",
    "post": ""
  }
}
```

### Game Launch Sequence
```
playGame(game, isCustom, backupOnClose, launchWithAdmin, specificExecutable)
    │
    ├─► Validate executable exists
    │
    ├─► Update Discord RPC ("Playing {game}")
    │
    ├─► Spawn game process
    │       │
    │       ├─► launchWithAdmin? → runas verb
    │       └─► Normal spawn
    │
    ├─► Hide Ascendara window (if setting enabled)
    │
    └─► Monitor process
            │
            └─► On exit:
                    ├─► Show Ascendara window
                    ├─► Update Discord RPC
                    └─► backupOnClose? → Run Ludusavi
```

## Settings System

### SettingsManager Class
```javascript
class SettingsManager {
  constructor() {
    this.filePath = path.join(app.getPath("userData"), "ascendarasettings.json");
    this.settings = this.loadSettings();
  }

  loadSettings() {
    // Load from file or return defaults
  }

  saveSettings(newSettings) {
    // Merge and write to file
    // Broadcast "settings-changed" event
  }

  updateSetting(key, value) {
    // Update single setting
  }
}
```

### Settings File Location
| Platform | Path |
|----------|------|
| Windows | `%APPDATA%/Ascendara by tagoWorks/ascendarasettings.json` |
| macOS | `~/Library/Application Support/Ascendara by tagoWorks/ascendarasettings.json` |
| Linux | `~/.config/Ascendara by tagoWorks/ascendarasettings.json` |

## Update System

### Update Flow
```
App Start
    │
    ├─► Check for updates (API call)
    │       │
    │       └─► New version available?
    │               │
    │               ├─► No: Continue normally
    │               │
    │               └─► Yes: Download in background
    │                       │
    │                       ├─► Verify checksum
    │                       │
    │                       └─► Notify user
    │                               │
    │                               └─► User clicks "Update"
    │                                       │
    │                                       └─► Install & restart
```

### Version Checking
```javascript
// Check against API
const response = await axios.get("https://api.ascendara.app/app/version");
const latestVersion = response.data.version;

if (semver.gt(latestVersion, currentVersion)) {
  // Update available
}
```

## Protocol Handler

### URL Format
```
ascendara://download?link={url}&game={name}&online={bool}&dlc={bool}&version={ver}&imgID={id}&size={bytes}
```

### Handler Flow
```
OS receives ascendara:// URL
    │
    ├─► App already running?
    │       │
    │       ├─► Yes: Send to existing instance via second-instance event
    │       │
    │       └─► No: Launch app with URL
    │
    └─► Parse URL parameters
            │
            └─► Trigger download flow
```

## Error Handling

### Error Propagation
```
Python Tool Error
    │
    ├─► Write error to JSON file
    │
    └─► Exit with non-zero code
            │
            ▼
Main Process
    │
    ├─► Detect error in progress file
    │
    └─► Send error event to renderer
            │
            ▼
Renderer
    │
    └─► Display error UI to user
```

### Error Codes
| Code | Meaning |
|------|---------|
| 0 | Success |
| 1 | General error |
| 2 | Network error |
| 3 | Extraction error |
| 4 | Verification failed |
| 5 | Disk space error |