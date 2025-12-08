# Project Structure

This document provides a detailed walkthrough of Ascendara's codebase organization.

## Root Directory

```
ascendara/
├── electron/           # Electron main process
├── src/                # React renderer process
├── binaries/           # Python tool source code
├── scripts/            # Build and utility scripts
├── public/             # Static assets
├── dist/               # Build output (generated)
├── build/              # Vite build output (generated)
├── package.json        # Node.js dependencies
├── requirements.txt    # Python dependencies
├── vite.config.js      # Vite configuration
├── tailwind.config.js  # TailwindCSS configuration
└── electron-builder.json5  # Electron builder config
```

---

## Electron Main Process (`electron/`)

The main process handles system-level operations, window management, and IPC communication.

```
electron/
├── app.js              # Main entry point
├── preload.js          # IPC bridge (contextBridge)
├── config.prod.js      # Production configuration
└── modules/            # Modular IPC handlers
    ├── index.js        # Module exports
    ├── config.js       # App configuration & constants
    ├── logger.js       # Logging utilities
    ├── settings.js     # SettingsManager class
    ├── encryption.js   # AES-256 encryption helpers
    ├── utils.js        # Utility functions
    ├── window.js       # Window management
    ├── downloads.js    # Download IPC handlers
    ├── games.js        # Game management handlers
    ├── updates.js      # Auto-update system
    ├── themes.js       # Theme import/export
    ├── system.js       # System utilities
    ├── tools.js        # Tool installation
    ├── steamcmd.js     # SteamCMD/Workshop
    ├── ludusavi.js     # Game save backups
    ├── discord-rpc.js  # Discord Rich Presence
    ├── protocol.js     # ascendara:// URL handling
    ├── translations.js # Language management
    ├── local-refresh.js # Local index refresh
    └── ipc-handlers.js # Miscellaneous handlers
```

### Key Files

#### `app.js`
The application entry point that:
- Creates the main BrowserWindow
- Initializes all modules
- Handles app lifecycle events (ready, activate, quit)
- Sets up the system tray
- Registers protocol handlers

#### `preload.js`
Secure bridge between main and renderer processes:
- Exposes `window.electron` API via contextBridge
- Organizes IPC methods by category
- Handles event subscriptions and cleanup

#### `modules/config.js`
Central configuration including:
- App version
- Environment detection (dev/prod)
- File paths (timestamp, languages, etc.)
- API keys and endpoints

#### `modules/settings.js`
The `SettingsManager` class that:
- Loads/saves user settings from `ascendarasettings.json`
- Provides getter/setter methods
- Broadcasts setting changes to renderer

---

## React Renderer (`src/`)

The frontend application built with React and Vite.

```
src/
├── main.jsx            # React entry point
├── App.jsx             # Root component & routing
├── index.css           # Global styles & Tailwind
├── pages/              # Page components
│   ├── Library.jsx     # Game library view
│   ├── Downloads.jsx   # Download manager
│   ├── Settings.jsx    # Settings page
│   ├── Search.jsx      # Game search
│   ├── GameInfo.jsx    # Game details page
│   ├── Welcome.jsx     # First-run wizard
│   └── ...
├── components/         # Reusable components
│   ├── ui/             # Base UI components
│   │   ├── button.jsx
│   │   ├── card.jsx
│   │   ├── dialog.jsx
│   │   └── ...
│   ├── Navbar.jsx      # Navigation bar
│   ├── GameCard.jsx    # Game display card
│   ├── DownloadCard.jsx # Download progress card
│   └── ...
├── context/            # React contexts
│   ├── SettingsContext.jsx
│   ├── DownloadContext.jsx
│   └── ...
├── hooks/              # Custom React hooks
│   ├── useSettings.js
│   ├── useDownloads.js
│   └── ...
├── lib/                # Utility libraries
│   └── utils.js        # Helper functions
└── public/             # Static assets
    ├── icon.png
    └── ...
```

### Key Patterns

#### Context Providers
Settings and downloads use React Context for global state:

```jsx
// SettingsContext.jsx
export const SettingsProvider = ({ children }) => {
  const [settings, setSettings] = useState(null);
  
  useEffect(() => {
    window.electron.getSettings().then(setSettings);
  }, []);
  
  return (
    <SettingsContext.Provider value={{ settings, setSettings }}>
      {children}
    </SettingsContext.Provider>
  );
};
```

#### IPC Usage in Components
Components communicate with the main process via `window.electron`:

```jsx
// Example: Starting a download
const handleDownload = async () => {
  await window.electron.downloadFile(
    link, game, online, dlc, isVr, 
    updateFlow, version, imgID, size, 
    additionalDirIndex, gameID
  );
};

// Example: Listening for progress
useEffect(() => {
  const unsubscribe = window.electron.onDownloadProgress((data) => {
    setProgress(data);
  });
  return unsubscribe;
}, []);
```

---

## Binary Tools (`binaries/`)

Python tools compiled to executables for production.

```
binaries/
├── AscendaraDownloader/
│   └── src/
│       └── AscendaraDownloader.py
├── AscendaraGameHandler/
│   └── src/
│       └── AscendaraGameHandler.py
├── AscendaraGofileHelper/
│   └── src/
│       └── AscendaraGofileHelper.py
├── AscendaraTorrentHandler/
│   └── src/
│       └── AscendaraTorrentHandler.py
├── AscendaraCrashReporter/
│   └── src/
│       └── AscendaraCrashReporter.py
├── AscendaraLanguageTranslation/
│   └── src/
│       └── AscendaraLanguageTranslation.py
├── AscendaraNotificationHelper/
│   └── src/
│       └── AscendaraNotificationHelper.py
└── AscendaraAchievementWatcher/
    └── src/
        └── watchdog.js  # Node.js (not Python)
```

### Tool Communication

Tools communicate with the main process via:

1. **JSON Files**: Progress updates written to `{game}.ascendara.json`
2. **Exit Codes**: Success/failure status
3. **Stdout/Stderr**: Logging and error messages

Example progress file structure:
```json
{
  "game": "GameName",
  "status": "downloading",
  "progress": 45.5,
  "speed": "25.3 MB/s",
  "eta": "2:30",
  "downloadingData": {
    "downloading": true,
    "extracting": false,
    "error": null
  }
}
```

---

## Build Scripts (`scripts/`)

```
scripts/
└── build_ascendara.py  # Automated build script
```

### `build_ascendara.py`

Automates the full build process:
1. Cleans previous build artifacts
2. Runs `yarn build` (Vite)
3. Modifies `index.html` for relative paths
4. Copies assets to `electron/` directory
5. Runs `electron-builder`
6. Cleans up temporary files

---

## Configuration Files

### `package.json`
Key scripts:
```json
{
  "scripts": {
    "dev": "vite --mode development",
    "build": "vite build",
    "start": "concurrently \"vite\" \"wait-on tcp:5173 && electron ./electron/app.js\"",
    "dist": "python3 ./scripts/build_ascendara.py",
    "buildwithelectron": "electron-builder --config.extraMetadata.main=electron/app.js"
  }
}
```

### `electron-builder.json5`
Electron builder configuration for:
- Windows NSIS installer
- macOS DMG
- Linux AppImage
- File associations
- Auto-update configuration

### `vite.config.js`
Vite configuration for:
- React plugin
- Build output settings
- Development server

### `tailwind.config.js`
TailwindCSS configuration for:
- Custom colors and themes
- Font families
- Animation utilities

---

## Data Files

Ascendara stores user data in platform-specific locations:

### Windows
```
%APPDATA%/Ascendara by tagoWorks/
├── ascendarasettings.json    # User settings
├── debug.log                 # Application logs
├── downloadmanager.log       # Download logs
└── gamehandler.log           # Game handler logs

%USERPROFILE%/
└── timestamp.ascendara.json  # App state & history

%LOCALAPPDATA%/Ascendara/
└── languages/                # Downloaded language files
```

### Download Directory (User-configured)
```
{downloadDirectory}/
├── games.json                # Custom games list
├── games/                    # Custom game images
│   └── {game}.ascendara.jpg
└── {GameName}/               # Individual game folders
    ├── {GameName}.ascendara.json  # Game metadata
    ├── filemap.ascendara.json     # File verification map
    └── ...game files...
```

---

## Development Workflow

### Starting Development
```bash
# Install dependencies
yarn install
pip install -r requirements.txt

# Start dev server
yarn start
```

### Building for Production
```bash
# Automated build
yarn dist

# Or manual steps
yarn build
yarn buildwithelectron
```

### Building Python Tools
```bash
cd binaries/AscendaraDownloader
pyinstaller --noconsole --onefile src/AscendaraDownloader.py
```

See [Creating the Tools](/docs/developer/creating-the-tools) for all PyInstaller commands.
