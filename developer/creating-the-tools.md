# Creating the Tools for Production Builds

For production deployment, Ascendara uses PyInstaller to bundle Python and all required packages into standalone executable files (.exe). This approach significantly simplifies the deployment process for new PCs using Ascendara, as users don't need to install Python or any dependencies separately.

## Building the Tools

To build each tool from Python source into an executable, you'll need to run specific PyInstaller commands. Here are the commands for each tool:

### Crash Reporter
```bash
pyinstaller --noconsole --onefile --icon=.\src\ascendara.ico --hidden-import unrar .\src\AscendaraCrashReporter.py
```

### Gofile Helper
```bash
pyinstaller --noconsole --add-binary=.\src\UnRAR.dll:. --onefile --icon=.\src\ascendara.ico --hidden-import unrar .\src\AscendaraGofileHelper.py
```

### Downloader
```bash
pyinstaller --noconsole --onefile --icon=.\src\ascendara.ico --hidden-import rarfile --hidden-import aiohttp --hidden-import aiofiles --add-data "src/UnRAR.dll;." .\src\AscendaraDownloader.py
```

### Game Handler
```bash
pyinstaller --noconsole --onefile --icon=.\src\ascendara.ico .\src\AscendaraGameHandler.py
```

### Language Translation
```bash
pyinstaller --noconsole --onefile --icon=.\src\ascendara.ico --hidden-import requests .\src\AscendaraLanguageTranslation.py
```

### Notification Helper
```bash
pyinstaller --noconsole --onefile --icon=.\src\ascendara.ico --add-data "src/ascendara.ico;." --hidden-import PyQt6 .\src\AscendaraNotificationHelper.py
```

### Achievement Watcher
> NOTE: This tool is written in JavaScript and is not a Python tool. It will require the `pkg` tool to be built into the executable for production.
```bash
pkg ./src/watchdog.js --targets node18-win-x64 --output ./dist/AscendaraAchievementWatcher.exe
```

### Torrent Handler
```bash
pyinstaller --noconsole --onefile --icon=.\src\ascendara.ico --hidden-import qbittorrentapi .\src\AscendaraTorrentHandler.py
```

## Command Options Explained

- `--noconsole`: Creates a Windows executable that doesn't show a console window
- `--onefile`: Bundles everything into a single executable
- `--icon`: Sets the icon for the executable
- `--hidden-import`: Includes specific Python packages that PyInstaller might not detect automatically
- `--add-binary`: Includes additional binary files (like UnRAR.dll) in the executable
- `--add-data`: Includes additional data files in the executable

The resulting executables will be created in the `dist` directory. Ascendara's `package.json` is configured to automatically look for these executables in their respective `dist` directories during the build process. As long as you run the PyInstaller commands from the project root (where the `src` directory is located), the executables will be created in the correct location and automatically bundled with Ascendara - no additional configuration or manual steps required.