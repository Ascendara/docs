# System Center and Command Palette

Ascendara includes a command palette for quick navigation and a System Center for diagnostics, storage, and recovery. None of these tools change how Ascendara stores your games.

## Command Palette

Press **Ctrl+K** (**Cmd+K** on macOS) anywhere in Ascendara to open the command palette. Type to search across:

- **Game Index**: Games you can download
- **Library**: Your installed games
- **Settings**: Every setting, so you can jump straight to it
- **Commands**: Ascendara actions

**Ctrl+F** opens the same search focused on the page you're on: your games in the Library, settings on the Settings page, and everything elsewhere. Use the arrow keys to move, **Enter** to select, and **Esc** to close.

### Commands

| Command | What it does |
| --- | --- |
| **Open System Center** | Opens the System Center. |
| **Run health check** | Opens the Health tab. |
| **Open Storage Manager** | Opens the Storage tab. |
| **Open Recovery tools** | Opens the Recovery tab. |
| **Create Settings Recovery Point** | Saves a snapshot of your current settings. |
| **Restore Latest Settings Recovery Point** | Restores the newest snapshot and reloads Ascendara. |
| **Rollback to Previous Ascendara Version** | Reinstalls the previous official release (Windows, Live branch only). |
| **Manage Game Profiles** | Opens [Game Profiles](/docs/features/library#game-profiles). |
| **Open Smart Collections** | Opens [Smart Collections](/docs/features/library#smart-collections). |
| **Enter Big Picture Mode** | Switches to [Big Picture Mode](/docs/features/big-picture). |
| **Refresh Local Index** | Opens [Your Game Index](/docs/features/refreshing-index). |
| **Open Settings** | Opens Settings. |
| **Reload interface** | Reloads the interface without changing your library or settings. |

## System Center

Open the System Center from the command palette with **Open System Center**. It has three tabs.

### Health

Checks your current configuration without changing it:

- **Settings**: Whether Ascendara can read its configuration.
- **Game directory**: Whether a download directory is set, whether Ascendara can write to it, and how much space is free.
- **Game Index**: Whether your local index or external source is available.
- **Ascendara services**: Whether the Monitor, API, CDN, LFS, and R2 endpoints are reachable. Local features keep working when some services are down.
- **Optional tools**: Which optional tools (sidecars) are installed.
- **Windows game dependencies**: Which game dependencies are installed (Windows).
- **UMU Launcher**: Whether UMU-Launcher is installed (Linux).
- **SteamCMD**: Whether SteamCMD is installed for the Workshop Downloader.

Each check that needs attention has a button to fix or configure it.

### Storage

Shows the space used by games, free space, and total capacity for each configured download location. Select **Manage folders** to change your download locations.

### Recovery

- **Safe UI Mode**: Reduces animations, transitions, and transparency effects. Use it if the interface renders incorrectly or is slow.
- **Reload interface**: Reloads the interface without changing your library or settings.
- **Clear browser data**: Clears Ascendara's Chromium cache, cookies, LocalStorage, and IndexedDB. You may be signed out. Your games and settings are not deleted.
- **Settings recovery points**: Snapshots of your settings, stored outside browser storage. Create one manually at any time. Ascendara also creates one automatically before installing an update and before a rollback. Restoring a point keeps settings that were added by newer versions.
- **Version rollback**: On Windows builds from the Live branch, Ascendara can reinstall the previous official GitHub release. It creates a settings recovery point first, downloads the official installer, and closes.
- **Developer diagnostics**: Opens DevTools for support and development builds.

## Other Support Tools

- **Server status**: The dot in the navigation bar shows whether Ascendara's servers are working. Click it for details on each service.
- **Upload Logs**: If the support team gives you a 6-digit support code, enter it in **Settings** to upload your logs. The logs include download and game-handler activity, your download directories and file paths, and your Windows username. They're deleted after your support case is resolved.
- **Report Issue**: Right-click anywhere in Ascendara and select **Report Issue**.
