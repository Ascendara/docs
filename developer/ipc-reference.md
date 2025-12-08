# IPC Reference

This document lists all IPC (Inter-Process Communication) channels available in Ascendara. These channels enable communication between the React renderer process and the Electron main process.

## How IPC Works in Ascendara

The `preload.js` file exposes a secure `window.electron` API to the renderer:

```javascript
// In your React component
const settings = await window.electron.getSettings();
```

Each method maps to an `ipcMain.handle()` in the main process modules.

---

## Window Management

| Method | Parameters | Returns | Description |
|--------|------------|---------|-------------|
| `minimizeWindow()` | - | `void` | Minimize the app window |
| `maximizeWindow()` | - | `void` | Maximize/restore the app window |
| `closeWindow()` | - | `void` | Close the app window |
| `toggleFullscreen()` | - | `void` | Toggle fullscreen mode |
| `getFullscreenState()` | - | `boolean` | Check if window is fullscreen |
| `clearCache()` | - | `boolean` | Clear application cache |
| `reload()` | - | `void` | Reload the application |

### Events
- `onWindowStateChange(callback)` - Fires when window maximized state changes

---

## Settings & Configuration

| Method | Parameters | Returns | Description |
|--------|------------|---------|-------------|
| `getSettings()` | - | `object` | Get all user settings |
| `saveSettings(options, directory)` | `object`, `string` | `boolean` | Save settings to file |
| `updateSetting(key, value)` | `string`, `any` | `boolean` | Update a single setting |
| `getDownloadDirectory()` | - | `string` | Get the download directory path |
| `getDefaultLocalIndexPath()` | - | `string` | Get default local index path |

### Crack/Emulator Settings
| Method | Parameters | Returns | Description |
|--------|------------|---------|-------------|
| `getLocalCrackUsername()` | - | `string\|null` | Get Steam emulator username |
| `setLocalCrackUsername(username)` | `string` | `boolean` | Set Steam emulator username |
| `getLocalCrackDirectory()` | - | `string\|null` | Get crack directory path |
| `setLocalCrackDirectory(directory)` | `string` | `boolean` | Set crack directory path |

### Events
- `onSettingsChanged(callback)` - Fires when settings are updated

---

## Game Management

| Method | Parameters | Returns | Description |
|--------|------------|---------|-------------|
| `getGames()` | - | `array` | Get all installed games |
| `getCustomGames()` | - | `array` | Get custom/imported games |
| `getInstalledGames()` | - | `array` | Get list of installed games |
| `getInstalledGamesSize()` | - | `number` | Get total size of installed games |
| `addGame(game, online, dlc, version, executable, imgID)` | `string`, `boolean`, `boolean`, `string`, `string`, `string` | `void` | Add a custom game |
| `removeCustomGame(game)` | `string` | `void` | Remove a custom game |
| `deleteGame(game)` | `string` | `void` | Delete a game and its files |
| `deleteGameDirectory(game)` | `string` | `void` | Delete only the game directory |
| `verifyGame(game)` | `string` | `object` | Verify game file integrity |
| `importSteamGames(directory)` | `string` | `boolean` | Import games from Steam directory |

### Game Images
| Method | Parameters | Returns | Description |
|--------|------------|---------|-------------|
| `updateGameCover(gameName, imgID, imageData)` | `string`, `string`, `string` | `boolean` | Update game cover image |
| `getGameImage(game)` | `string` | `string\|null` | Get game image as base64 |
| `getLocalImageUrl(imagePath)` | `string` | `string\|null` | Get local image as data URL |

### Game Backups (Ludusavi)
| Method | Parameters | Returns | Description |
|--------|------------|---------|-------------|
| `ludusavi(action, game)` | `string`, `string` | `object` | Execute ludusavi backup/restore |
| `enableGameAutoBackups(game, isCustom)` | `string`, `boolean` | `boolean` | Enable auto-backups for game |
| `disableGameAutoBackups(game, isCustom)` | `string`, `boolean` | `boolean` | Disable auto-backups for game |
| `isGameAutoBackupsEnabled(game, isCustom)` | `string`, `boolean` | `boolean` | Check if auto-backups enabled |
| `gameRated(game, isCustom)` | `string`, `boolean` | `boolean` | Mark game as rated |

### Game Executables
| Method | Parameters | Returns | Description |
|--------|------------|---------|-------------|
| `getGameExecutables(game, isCustom)` | `string`, `boolean` | `array` | Get list of game executables |
| `setGameExecutables(game, executables, isCustom)` | `string`, `array`, `boolean` | `boolean` | Set game executables |
| `modifyGameExecutable(game, executable)` | `string`, `string` | `boolean` | Change default executable |
| `createGameShortcut(game)` | `string` | `boolean` | Create desktop shortcut |
| `getLaunchCommands(game, isCustom)` | `string`, `boolean` | `object\|null` | Get launch commands |
| `saveLaunchCommands(game, launchCommands, isCustom)` | `string`, `object`, `boolean` | `boolean` | Save launch commands |
| `readGameAchievements(game, isCustom)` | `string`, `boolean` | `object\|null` | Read game achievements |

---

## Game Execution

| Method | Parameters | Returns | Description |
|--------|------------|---------|-------------|
| `playGame(game, isCustom, backupOnClose, launchWithAdmin, specificExecutable)` | `string`, `boolean`, `boolean`, `boolean`, `string` | `boolean` | Launch a game |
| `isGameRunning(game)` | `string` | `boolean` | Check if game is running |
| `startSteam()` | - | `boolean` | Launch Steam client |
| `isSteamRunning()` | - | `boolean` | Check if Steam is running |

---

## Downloads

| Method | Parameters | Returns | Description |
|--------|------------|---------|-------------|
| `downloadFile(link, game, online, dlc, isVr, updateFlow, version, imgID, size, additionalDirIndex, gameID)` | multiple | `void` | Start a game download |
| `stopDownload(game, deleteContents)` | `string`, `boolean` | `boolean` | Stop an active download |
| `retryDownload(link, game, online, dlc, version)` | `string`, `string`, `boolean`, `boolean`, `string` | `boolean` | Retry a failed download |
| `checkRetryExtract(game)` | `string` | `boolean` | Check if extraction can be retried |
| `retryExtract(game, online, dlc, version)` | `string`, `boolean`, `boolean`, `string` | `void` | Retry file extraction |
| `downloadItem(url)` | `string` | `object` | Download a Steam Workshop item |
| `downloadSoundtrack(track, game)` | `string`, `string` | `boolean` | Download game soundtrack |
| `isDownloaderRunning()` | - | `boolean` | Check if any download is active |
| `getDownloadHistory()` | - | `array` | Get download history |

### Events
- `onDownloadProgress(callback)` - Fires with download progress updates
- `onDownloadComplete(callback)` - Fires when download completes

---

## File & Directory Management

| Method | Parameters | Returns | Description |
|--------|------------|---------|-------------|
| `openGameDirectory(game, isCustom)` | `string`, `boolean` | `void` | Open game folder in explorer |
| `openDirectoryDialog()` | - | `string\|null` | Show folder picker dialog |
| `openFileDialog(exePath)` | `string\|null` | `string\|null` | Show file picker dialog |
| `canCreateFiles(directory)` | `string` | `boolean` | Check write permissions |
| `checkFileExists(filePath)` | `string` | `boolean` | Check if file exists |
| `getDriveSpace(path)` | `string` | `object` | Get drive space info |
| `getAssetPath(filename)` | `string` | `string` | Get path to bundled asset |

### Events
- `onDirectorySizeStatus(callback)` - Fires with directory size calculation progress

---

## Tools & Dependencies

| Method | Parameters | Returns | Description |
|--------|------------|---------|-------------|
| `getInstalledTools()` | - | `object` | Get status of installed tools |
| `installTool(tool)` | `string` | `boolean` | Install a specific tool |
| `installDependencies()` | - | `boolean` | Install game dependencies |
| `installPython()` | - | `object` | Install Python (macOS/Linux) |
| `installWine()` | - | `object` | Install Wine (macOS/Linux) |
| `isSteamCMDInstalled()` | - | `boolean` | Check if SteamCMD installed |
| `installSteamCMD()` | - | `boolean` | Install SteamCMD |
| `checkGameDependencies()` | - | `object` | Check for missing dependencies |
| `openReqPath(game)` | `string` | `void` | Open required libraries folder |
| `folderExclusion(boolean)` | `boolean` | `object` | Manage Windows Defender exclusions |
| `isWatchdogRunning()` | - | `boolean` | Check if achievement watcher running |

---

## Updates

| Method | Parameters | Returns | Description |
|--------|------------|---------|-------------|
| `checkForUpdates()` | - | `object\|null` | Check for app updates |
| `updateAscendara()` | - | `void` | Install pending update |
| `isUpdateDownloaded()` | - | `boolean` | Check if update is ready |
| `isBrokenVersion()` | - | `boolean` | Check if version is broken |
| `deleteInstaller()` | - | `void` | Delete downloaded installer |
| `uninstallAscendara()` | - | `void` | Uninstall the application |

### Events
- `onUpdateAvailable(callback)` - Fires when update is available
- `onUpdateReady(callback)` - Fires when update is downloaded

---

## Themes & UI

| Method | Parameters | Returns | Description |
|--------|------------|---------|-------------|
| `getBackgrounds()` | - | `array` | Get available backgrounds |
| `setBackground(color, gradient)` | `string`, `string` | `void` | Set background color/gradient |
| `saveCustomThemeColors(customTheme)` | `object` | `boolean` | Save custom theme |
| `exportCustomTheme(customTheme)` | `object` | `object` | Export theme to file |
| `importCustomTheme()` | - | `object` | Import theme from file |

---

## Discord RPC

| Method | Parameters | Returns | Description |
|--------|------------|---------|-------------|
| `toggleDiscordRPC(enabled)` | `boolean` | `object` | Enable/disable Discord RPC |
| `switchRPC(state)` | `string` | `void` | Change RPC display state |

---

## Language & Translations

| Method | Parameters | Returns | Description |
|--------|------------|---------|-------------|
| `downloadLanguage(langCode)` | `string` | `boolean` | Download a language pack |
| `saveLanguageFile(langCode, content)` | `string`, `object` | `boolean` | Save language file |
| `getLanguageFile(langCode)` | `string` | `object\|null` | Get language file content |
| `startTranslation(langCode)` | `string` | `boolean` | Start AI translation |
| `cancelTranslation()` | - | `boolean` | Cancel active translation |
| `getDownloadedLanguages()` | - | `array` | Get list of downloaded languages |
| `languageFileExists(filename)` | `string` | `boolean` | Check if language file exists |

---

## Local Index Refresh

| Method | Parameters | Returns | Description |
|--------|------------|---------|-------------|
| `startLocalRefresh(data)` | `object` | `object` | Start local index refresh |
| `stopLocalRefresh(outputPath)` | `string` | `object` | Stop local index refresh |
| `sendLocalRefreshCookie(cookie)` | `string` | `object` | Send cookie for authentication |
| `getLocalRefreshProgress(outputPath)` | `string` | `object\|null` | Get refresh progress |
| `getLocalRefreshStatus(outputPath)` | `string` | `object` | Get refresh status |

### Events
- `onLocalRefreshProgress(callback)` - Progress updates
- `onLocalRefreshComplete(callback)` - Refresh completed
- `onLocalRefreshError(callback)` - Error occurred
- `onLocalRefreshCookieNeeded(callback)` - Cookie required

---

## System & Platform

| Method | Parameters | Returns | Description |
|--------|------------|---------|-------------|
| `getPlatform()` | - | `string` | Get OS platform (win32/darwin/linux) |
| `isOnWindows()` | - | `boolean` | Check if running on Windows |
| `fetchSystemSpecs()` | - | `object` | Get system specifications |
| `isDev()` | - | `boolean` | Check if in development mode |
| `isExperiment()` | - | `boolean` | Check if experimental build |
| `switchBuild(buildType)` | `string` | `boolean` | Switch between stable/experimental |
| `showTestNotification()` | - | `object` | Show a test notification |

---

## API & Networking

| Method | Parameters | Returns | Description |
|--------|------------|---------|-------------|
| `getAPIKey()` | - | `string` | Get API key |
| `getAnalyticsKey()` | - | `string` | Get analytics key |
| `getImageKey()` | - | `string` | Get image signing key |
| `openURL(url)` | `string` | `void` | Open URL in default browser |
| `fetchApiImage(endpoint, imgID, timestamp, signature)` | `string`, `string`, `number`, `string` | `object` | Fetch signed API image |
| `request(url, options)` | `string`, `object` | `Promise<object>` | Make HTTPS request |

---

## Support & Profile

| Method | Parameters | Returns | Description |
|--------|------------|---------|-------------|
| `uploadSupportLogs(sessionToken, appToken)` | `string`, `string` | `object` | Upload logs for support |
| `uploadProfileImage(imageBase64)` | `string` | `boolean` | Upload profile image |
| `getProfileImage()` | - | `string\|null` | Get profile image |

---

## Welcome Flow & App State

| Method | Parameters | Returns | Description |
|--------|------------|---------|-------------|
| `isNew()` | - | `boolean` | Check if first launch |
| `isV7()` | - | `boolean` | Check if v7 welcome shown |
| `setV7()` | - | `void` | Mark v7 welcome as shown |
| `checkV7Welcome()` | - | `boolean` | Check if should show v7 welcome |
| `hasLaunched()` | - | `boolean` | Check if app has launched before |
| `hasAdmin()` | - | `boolean` | Check if has admin privileges |
| `updateLaunchCount()` | - | `void` | Increment launch counter |
| `getLaunchCount()` | - | `number` | Get total launch count |
| `createTimestamp()` | - | `void` | Create timestamp file |
| `setTimestampValue(key, value)` | `string`, `any` | `void` | Set timestamp value |
| `getTimestampValue(key)` | `string` | `any` | Get timestamp value |
| `timestampTime()` | - | `string` | Get formatted timestamp |

### Events
- `onWelcomeComplete(callback)` - Welcome flow completed
- `triggerWelcomeComplete()` - Trigger welcome complete event

---

## Low-Level IPC Access

For advanced use cases, direct IPC access is available:

```javascript
// Direct channel access
window.electron.ipcRenderer.invoke("channel-name", ...args);
window.electron.ipcRenderer.on("event-name", callback);
window.electron.ipcRenderer.off("event-name", callback);

// File operations
window.electron.ipcRenderer.readFile(path);
window.electron.ipcRenderer.writeFile(path, content);
```

> **Warning**: Direct IPC access bypasses the typed API. Use the documented methods when possible.
