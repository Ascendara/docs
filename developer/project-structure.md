# Project Structure

This document walks through how Ascendara's codebase is organized.

## Root Directory

```
ascendara/
├── electron/             # Electron main process
├── src/                  # React renderer
├── binaries/             # Helper tools (Python, Rust, Node.js)
├── scripts/              # Build, release, and maintenance scripts
├── tests/                # Python tests for build and release scripts
├── docs/                 # Maintainer docs (BUILD_AUTOMATION.md)
├── packaging/winget/     # Windows Package Manager manifests
├── readme/               # README images and app icons (ico, icns, png)
├── .github/workflows/    # CI: source checks, releases, release notes, WinGet
├── build/                # Vite output (generated)
├── dist/                 # Packaged app output (generated)
├── package.json          # Dependencies, scripts, electron-builder config
├── requirements.txt      # Python dependencies for development
├── vite.config.js        # Vite configuration
├── tailwind.config.js    # Tailwind CSS configuration
├── eslint.config.js      # ESLint configuration
├── .prettierrc           # Prettier configuration
├── .env.example          # Example environment (Firebase, build secrets)
├── README_LINUX_BUILD.md # Linux build notes
└── README_MAC_BUILD.md   # macOS build notes
```

The electron-builder configuration lives in the `build` section of `package.json`.

---

## Electron Main Process (`electron/`)

```
electron/
├── app.js                   # Entry point
├── preload.js               # window.electron API (contextBridge)
├── config.prod.js           # Production config (not in source checkouts)
├── build-signature.json     # Generated at build time
├── sounds/, assets/         # Copied from the renderer build when packaging
└── modules/
    ├── index.js             # Loads modules, installs the IPC guard, lazy loading
    ├── config.js            # Version, branch, platform, paths, helper paths
    ├── settings.js          # SettingsManager
    ├── encryption.js        # AES-256-CBC for sensitive settings
    ├── security.js          # Trusted-sender guard for every ipcMain.handle
    ├── preload-bridge.js    # Event subscriptions and the legacy IPC bridge
    ├── renderer-network.js  # Restricted HTTP requests for the renderer
    ├── local-api-proxy.js   # Proxy routes on the local server (TorBox, KHInsider, FLiNG)
    ├── local-server-security.js
    ├── auth-helper.js       # Signed API request headers
    ├── build-signature-loader.js
    ├── logger.js            # debug.log
    ├── utils.js
    ├── window.js            # Window, tray, close behavior
    ├── protocol.js          # ascendara:// handling, single instance
    ├── ipc-handlers.js      # Miscellaneous handlers
    ├── downloads.js         # Downloader / torrent handler processes
    ├── downloaderRuntime.js
    ├── games.js             # Library, launching, executables, shortcuts
    ├── gameDirectories.js   # De-duplicates download directories
    ├── launcher-discovery.js, launcher-import.js   # Import from other launchers
    ├── local-refresh.js     # Game index IPC
    ├── community-index.js   # Game index sync and covers
    ├── index-relay.js       # Helps the API refresh a blocked index
    ├── steamgrid.js         # SteamGridDB artwork (via API proxy)
    ├── hltb.js              # HowLongToBeat
    ├── ludusavi.js          # Save backups
    ├── steamcmd.js          # Workshop Downloader
    ├── tools.js             # Sidecar installation
    ├── system.js            # Specs, drive space, dependencies, Defender exclusions
    ├── system-power.js      # Lock, sleep, shutdown after downloads
    ├── dependency-installer.js
    ├── proton.js            # Linux runners, prefixes, Proton/UMU downloads
    ├── umu-database.js      # UMU game IDs
    ├── wine-setup.js        # macOS Wine detection and setup
    ├── updates.js           # Update checks and downloads, branch switching
    ├── install-update.js    # Installs updates on each platform
    ├── recovery.js          # Settings recovery points, version rollback
    ├── onboarding.js        # Setup completion state
    ├── themes.js            # Theme import/export
    ├── translations.js      # Language downloads and translation tool
    ├── discord-rpc.js       # Discord Rich Presence
    ├── qrcode.js            # Companion QR codes
    └── retro/               # Ascendara Retro
        ├── index.js         # IPC, library state, launching
        ├── platforms.js     # Console definitions and launch presets
        ├── scanner.js       # ROM folder scanning
        ├── catalogue-worker.js  # LaunchBox metadata import
        ├── saves.js         # Save backups and memory cards
        └── expanded-catalogue.json
```

### Key Files

#### `app.js`
- Enforces a single instance and registers the `ascendara://` protocol
- Starts the local HTTP server that serves the renderer in packaged builds (`127.0.0.1:46859`)
- Creates the main window and system tray
- Starts the Achievement Watcher
- Registers handlers for uncaught errors

#### `preload.js`
Exposes the `window.electron` API with `contextBridge`. Methods are grouped by area (window, settings, games, downloads, retro, recovery, and so on). A separate `window.qbittorrentApi` object is exposed for qBittorrent checks. See the [IPC Reference](/docs/developer/ipc-reference).

#### `modules/settings.js`
The `SettingsManager` loads and saves `ascendarasettings.json`, fills in default values, encrypts sensitive keys, and notifies the renderer with a `settings-updated` event when settings change.

---

## React Renderer (`src/`)

```
src/
├── main.jsx              # React entry point
├── app.jsx               # Routes, global providers, background services
├── app.css               # Global styles and Tailwind
├── pages/
│   ├── Home.jsx, Search.jsx, Download.jsx
│   ├── Library.jsx, FolderView.jsx, GameScreen.jsx
│   ├── Downloads.jsx, TorboxDownloads.jsx
│   ├── Settings.jsx, SidecarAndDependencies.jsx, ExtraLanguages.jsx
│   ├── LocalRefresh.jsx  # Your Game Index
│   ├── Retro.jsx, Profile.jsx, WorkshopDownloader.jsx
│   ├── Welcome.jsx       # First-run setup
│   ├── Ascend.jsx + ascend/        # Ascend sections and settings cards
│   └── BigPicture.jsx + big-picture/   # Big Picture surfaces and controller input
├── components/
│   ├── ui/               # shadcn/ui primitives
│   ├── download/         # Download page parts
│   ├── gamescreen/       # Game page tabs
│   ├── feature-centers/  # Shared UI for System Center, Game Profiles, Smart Collections
│   ├── Navigation.jsx, MenuBar.jsx, Layout.jsx
│   ├── GlobalSearch.jsx  # Command palette
│   ├── SystemCenter.jsx, GameProfilesCenter.jsx, SmartCollectionsCenter.jsx
│   └── …
├── context/              # Auth, Language, Search, Settings, Theme, Tour
├── hooks/                # Image loading, search, command palette, Retro access, …
├── services/             # Firebase, game info, downloads, TorBox, Nexus Mods, …
├── lib/                  # Utilities, settings search data, sanitizing
├── config/providers.js   # Download provider definitions
├── i18n/                 # Feature-center translations
├── translations/         # en.json and the built-in languages
├── utils/                # Ascend access and auth helpers
├── styles/
└── public/               # Static assets and sounds
```

### Key Patterns

#### Settings Context
Settings are loaded once and shared through `SettingsContext`:

```jsx
import { useSettings } from "@/context/SettingsContext";

const { settings, updateSetting } = useSettings();
await updateSetting("downloadLimit", 0);
```

#### Translations
All UI text comes from the language files:

```jsx
import { useLanguage } from "@/context/LanguageContext";

const { t } = useLanguage();
return <h1>{t("library.pageTitle")}</h1>;
```

#### IPC Usage in Components
Components call the main process through `window.electron`:

```jsx
// Start a download
await window.electron.downloadFile(
  link, game, online, dlc, isVr,
  updateFlow, version, imgID, size,
  additionalDirIndex, gameID
);

// Subscribe to an event; the returned function unsubscribes
useEffect(() => {
  const unsubscribe = window.electron.onGameClosed((_, data) => setRunning(false));
  return unsubscribe;
}, []);
```

---

## Helper Tools (`binaries/`)

```
binaries/
├── AscendaraDownloader/src/
│   ├── AscendaraDownloader.py        # CLI and installation pipeline
│   ├── AscendaraTransfer.py          # HTTP transfers
│   ├── AscendaraGofile.py            # Gofile resolver
│   ├── AscendaraExtraction.py        # Archive extraction worker
│   ├── AscendaraDownloadRecovery.py  # Retries and repair downloads
│   ├── AscendaraRarRecovery.py       # UnRAR bindings
│   └── UnRAR.dll
├── AscendaraGameHandler/src/AscendaraGameHandler.py
├── AscendaraTorrentHandler/src/AscendaraTorrentHandler.py
├── AscendaraNotificationHelper/src/AscendaraNotificationHelper.py
├── AscendaraLanguageTranslation/src/AscendaraLanguageTranslation.py
├── AscendaraLocalRefresh/src/        # Index scraper (not shipped)
│   ├── AscendaraLocalRefresh.py, base_scraper.py
│   ├── steamrip_scraper.py, goggames_scraper.py, utils.py
├── AscendaraCrashReporter/           # Rust (Cargo.toml, src/main.rs, build.rs)
└── AscendaraAchievementWatcher/      # Node.js (src/watchdog.js, monitor.js, …)
```

Each Python tool has its own `requirements.txt`. Built executables go to `binaries/<Tool>/dist/` (Rust: `target/release/`).

### Tool Communication

Tools communicate with the app through:

1. **JSON files**: The Downloader and Torrent Handler write progress to the `downloadingData` object in `{game}.ascendara.json`. The Game Handler updates `isRunning`, `playTime`, and `launchCount` there (or in `games.json` for custom games).
2. **Command-line arguments**: The app passes everything a tool needs when it starts it.
3. **Exit codes and logs**: For errors and diagnostics.

Example `downloadingData` during a download:

```json
{
  "game": "GameName",
  "version": "1.0.0",
  "downloadingData": {
    "downloading": true,
    "extracting": false,
    "verifying": false,
    "updating": false,
    "progressCompleted": "45.50",
    "progressDownloadSpeeds": "25.3 MB/s",
    "timeUntilComplete": "2m 30s"
  }
}
```

See the [Downloader](/docs/binary-tool/downloader#state-and-files) documentation for every field.

---

## Scripts (`scripts/`)

| Script | Purpose |
| --- | --- |
| `build_ascendara.py` | Windows app build (`yarn dist`) |
| `build_windows_helpers.py` | Builds the Windows helper executables |
| `build_ascendara_linux.py` | Linux build, including helpers (`yarn dist-linux`) |
| `build_ascendara_mac.py` | macOS build (`yarn dist-mac`) |
| `setup_macos_tools.py` | Downloads the official macOS 7-Zip (`yarn setup-mac`) |
| `build_support.py` | Shared resource validation and build registration |
| `generate_build_signature.js` | Creates `electron/build-signature.json` |
| `register_build.py` | Registers a build with the backend (maintainers only) |
| `ci_build.py`, `ci_release.py` | Used by the release workflow |
| `release_notes.py`, `winget_release.py` | Release descriptions and WinGet submissions |
| `copy_debian_scripts.py` | Copies Python sources for Linux packaging |
| `check_unused_translations.py` | Finds unused translation keys |
| `count_lines.py` | Code statistics |

---

## Configuration Files

### `package.json`
Key scripts:
```json
{
  "scripts": {
    "dev": "vite --mode development",
    "build": "vite build",
    "start": "concurrently \"vite\" \"wait-on tcp:5173 && electron ./electron/app.js --no-sandbox\"",
    "dist": "python3 ./scripts/build_ascendara.py",
    "dist-linux": "python3 ./scripts/build_ascendara_linux.py",
    "dist-mac": "python3 ./scripts/build_ascendara_mac.py",
    "lint": "eslint src electron binaries/AscendaraAchievementWatcher/src scripts/*.cjs tests/*.cjs",
    "format": "prettier --write ."
  }
}
```

The `build` section configures electron-builder: NSIS for Windows, DMG and ZIP for macOS, AppImage for Linux, and the helper binaries copied into each package as `extraResources`.

### `vite.config.js`
The React plugin, the `@` alias for `src/`, the development server, and build output settings.

### `tailwind.config.js`
Theme colors, fonts, and animations.

---

## Data Files

Ascendara stores user data in these locations:

| Data | Windows | macOS | Linux |
| --- | --- | --- | --- |
| Settings (`ascendarasettings.json`) | `%APPDATA%\ascendara\` | `~/Library/Application Support/ascendara/` | `~/.config/ascendara/` |
| App state (`timestamp.ascendara.json`) | `%USERPROFILE%\` | `~/` | `~/` |
| App log (`debug.log`) | `%APPDATA%\Ascendara by tagoWorks\` | `~/Library/Application Support/Ascendara by tagoWorks/` | `~/.config/Ascendara by tagoWorks/` |
| Extra languages | `%LOCALAPPDATA%\Ascendara\languages\` | `~/Library/Application Support/ascendara/languages/` | `~/.ascendara/languages/` |
| Game index | `%APPDATA%\ascendara\localindex\` | `~/Library/Application Support/ascendara/localindex/` | `~/.config/ascendara/localindex/` |
| Retro library | `%APPDATA%\ascendara\retro\` | `~/Library/Application Support/ascendara/retro/` | `~/.config/ascendara/retro/` |
| Settings recovery points | `%APPDATA%\ascendara\recovery-points\` | `~/Library/Application Support/ascendara/recovery-points/` | `~/.config/ascendara/recovery-points/` |
| Custom Lists | `Documents\Ascendara\CustomLists\` | `~/Documents/Ascendara/CustomLists/` | `~/Documents/Ascendara/CustomLists/` |

On Linux, prefixes, runners, and tools live in `~/.ascendara/`. See [Linux and macOS](/docs/features/linux-and-macos#linux-files).

### Download Directory (User-Configured)
```
{downloadDirectory}/
├── games.json                     # Custom and imported games
└── {GameName}/                    # One folder per downloaded game
    ├── {GameName}.ascendara.json  # Game metadata and download state
    ├── filemap.ascendara.json     # Installed-file manifest for verification
    └── …game files…
```

---

## Development Workflow

```bash
# Install dependencies
yarn install
pip install -r requirements.txt

# Start the development app
yarn start

# Build for your platform
yarn dist --skip-registration          # Windows (after scripts/build_windows_helpers.py)
yarn dist-linux --skip-registration    # Linux
yarn dist-mac --skip-registration      # macOS
```

See [Building from Source](/docs/developer/build-from-source) and [Creating the Tools](/docs/developer/creating-the-tools).
