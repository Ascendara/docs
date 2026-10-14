# IPC Reference

This document lists the API that `electron/preload.js` exposes to the renderer as `window.electron`. Each method calls an `ipcMain.handle()` handler in the main process, usually with the same name in kebab-case (for example, `getSettings()` → `get-settings`).

## How IPC Works in Ascendara

```javascript
// In a React component
const settings = await window.electron.getSettings();

// Events: subscribe, and call the returned function to unsubscribe
const unsubscribe = window.electron.onGameClosed((_, { game }) => {
  console.log(`${game} closed`);
});
unsubscribe();
```

- Methods return a Promise unless noted otherwise.
- Event callbacks never receive the Electron event object. Callbacks marked with `(_, data)` receive a placeholder as their first argument for compatibility.
- Every handler checks that the call comes from Ascendara's own window (see [Security](/docs/developer/system-design#security)).

---

## Window Management

| Method | Description |
|--------|-------------|
| `minimizeWindow()` | Minimize the window |
| `maximizeWindow()` | Maximize or restore the window |
| `closeWindow(forceQuit)` | Close the window. Hides to the tray unless `forceQuit` is `true` or the app is set to quit on close |
| `toggleFullscreen()` / `setFullscreen(value)` | Change fullscreen mode |
| `getFullscreenState()` / `isWindowMaximized()` | Read the window state |
| `shutdownSystem()` | Shut down the computer |
| `clearCache()` | Clear the application cache |
| `openDevTools()` | Open DevTools |
| `reload()` | Reload the renderer |

**Events**: `onWindowStateChange(callback)`, `onAppClose(callback)`, `onAppHidden(callback)`, `onAppShown(callback)`

---

## Settings & Configuration

| Method | Description |
|--------|-------------|
| `getSettings()` | Get all settings (sensitive keys decrypted) |
| `saveSettings(options, directory)` | Save settings |
| `updateSetting(key, value)` | Update a single setting |
| `getDownloadDirectory()` | Get the download directory |
| `getDefaultLocalIndexPath()` | Get the default game index folder |
| `getSteamApiKey()` | Legacy; Steam requests now go through the Ascendara API |

**Events**: `onSettingsChanged((_, settings) => …)`

### Goldberg Name

| Method | Description |
|--------|-------------|
| `getLocalCrackUsername()` / `setLocalCrackUsername(username)` | Get or set the Goldberg emulator username |
| `getLocalCrackDirectory()` / `setLocalCrackDirectory(directory)` | Get or set the Goldberg settings directory |

### App State (Timestamp File)

| Method | Description |
|--------|-------------|
| `createTimestamp()` | Create `timestamp.ascendara.json` |
| `getTimestampValue(key)` / `setTimestampValue(key, value)` | Read or write a value |
| `timestampTime()` | Get the formatted timestamp |

### External Sources and Custom Lists

| Method | Description |
|--------|-------------|
| `getExternalSourcesDirectory()` | Folder for external source JSON (`<localIndex>/external-sources`) |
| `getExternalSourceJson(sourceId)` / `setExternalSourceJson(sourceId, data)` / `removeExternalSourceJson(sourceId)` | Manage a stored external source |
| `fetchCustomSource(url)` | Fetch an external source over HTTPS |
| `getCustomListsDirectory()` | Folder for Custom Lists (`Documents/Ascendara/CustomLists`) |
| `getCustomListData(listId)` / `setCustomListData(listId, data)` / `removeCustomListData(listId)` | Manage a Custom List |
| `getCustomListFilePath(listId)` / `openCustomListFile(listId)` / `showCustomListInFolder(listId)` | Locate a Custom List file |

---

## Welcome Flow & App State

| Method | Description |
|--------|-------------|
| `isNew()` | Whether this is the first launch |
| `hasLaunched()` | Whether the app has launched before |
| `isV7()` / `setV7()` / `checkV7Welcome()` | Legacy version 7 welcome state |
| `hasAdmin()` | Whether Ascendara is running as administrator |
| `updateLaunchCount()` / `getLaunchCount()` | App launch counter |
| `triggerWelcomeComplete()` | Signal that the welcome flow finished |
| `getHardwareId()` | Hardware ID used for Ascend trial verification |

**Events**: `onWelcomeComplete(callback)`

---

## Local Game Index

| Method | Description |
|--------|-------------|
| `syncCommunityIndex()` | Sync the local index with the official one if it changed |
| `downloadSharedIndex(outputPath)` | Force a fresh download of the official index into `outputPath` (runs in the background) |
| `getPublicIndexDownloadStatus()` | Whether a forced download is in progress |
| `getIndexCover(imgID)` | Get a cover as a data URL, downloading it if needed |
| `getIndexStatus()` | The API's index status (`serverRefresh`, `blocked`, `lastSuccess`, …) |
| `startIndexRelay()` | Help the API refresh a blocked index, then sync |
| `getLocalRefreshProgress(outputPath)` | Read `progress.json` in the index folder |

**Events**: `onPublicIndexDownloadStarted`, `onPublicIndexDownloadProgress`, `onPublicIndexDownloadComplete`, `onPublicIndexDownloadError`, each with a matching `off…` method to remove all listeners. The renderer also receives a `window` `index-refreshed` DOM event when the index changes.

---

## Game Management

| Method | Description |
|--------|-------------|
| `getGames()` | Downloaded games |
| `getCustomGames()` | Custom and imported games |
| `getInstalledGames()` / `getInstalledGamesSize()` | Installed games and their total size |
| `addGame(game, online, dlc, version, executable, imageUrl)` | Add a custom game |
| `removeCustomGame(game, installation)` | Remove a custom game from the library |
| `deleteGame(game, installation)` | Delete a game and its files |
| `deleteGameDirectory(game)` | Delete only the game's folder |
| `saveDeletedGameData(game, installation)` / `restoreDeletedGameData(game)` / `discardDeletedGameData(game)` | Manage History (data kept from deleted games) |
| `checkGameDirectoryConflict(game, additionalDirIndex)` / `renameExistingGameDirectory(game, additionalDirIndex)` | Handle an existing folder before downloading |
| `verifyGame(game)` | Verify a game's files against its file map |
| `scanGameFolders()` | Scan a folder for games to add |
| `readGameEntry(game, isCustom)` / `writeGameEntry(game, updatedData, isCustom)` | Read or write a game's raw data |
| `restoreCloudGameData(gameName, cloudData)` | Restore playtime and stats from the Ascend cloud library |

### Importing From Launchers

| Method | Description |
|--------|-------------|
| `getLauncherImportState()` | Current import state |
| `startLauncherImport(launchers)` | Start importing from the given launchers |
| `cancelLauncherImport()` | Stop the import queue |

**Events**: `onLauncherImportProgress(callback)`

### Images and Assets

| Method | Description |
|--------|-------------|
| `getGameImage(game, type)` | Get a game image |
| `updateGameCover(gameName, imgID, imageData)` | Replace a game's cover |
| `saveGameAsset(gameName, filename, dataUrl)` | Save an artwork file for a game |
| `ensureGameAssets(gameName)` / `repairGameImage(game)` | Download missing artwork |
| `getLocalImageUrl(imagePath)` | Read a local image as a data URL |
| `getSteamGridUrls(gameName)` / `getSteamGridHeader(gameName)` | SteamGridDB artwork |

**Events**: `onGameAssetsUpdated(callback)`, `onCoverImageUpdated((_, data) => …)`

### Executables, Shortcuts, and Launch Options

| Method | Description |
|--------|-------------|
| `getGameExecutables(game, isCustom)` / `setGameExecutables(game, executables, isCustom)` | Manage a game's executables (the first is the primary) |
| `modifyGameExecutable(game, executable)` | Change the primary executable |
| `getLaunchCommands(game, isCustom)` / `saveLaunchCommands(game, launchCommands, isCustom)` | Launch options |
| `createGameShortcut(game)` / `createStartMenuShortcut(game)` | Desktop and Start Menu shortcuts (Windows) |

### Achievements

| Method | Description |
|--------|-------------|
| `readGameAchievements(game, isCustom)` | Read a game's achievements |
| `writeGameAchievements(gameName, achievements)` | Write a game's achievements |
| `getAchievementsLeaderboard(games, options)` | Achievement stats for the profile |

### Save Backups (Ludusavi)

| Method | Description |
|--------|-------------|
| `ludusavi(action, game, backupName)` | Run a Ludusavi action (`backup`, `restore`, `list-backups`, …) |
| `enableGameAutoBackups(game, isCustom)` / `disableGameAutoBackups(game, isCustom)` / `isGameAutoBackupsEnabled(game, isCustom)` | Auto-backup on game close |
| `getCustomSavePaths(gameName, isCustomGame)` / `setCustomSavePaths(gameName, isCustomGame, paths)` | Custom save paths |
| `listBackupFiles(dirPath)` / `readBackupFile(filePath)` | Read local backup files (for cloud upload) |
| `gameRated(game, isCustom)` | Mark a game as rated |

---

## Game Execution

| Method | Description |
|--------|-------------|
| `playGame(game, isCustom, backupOnClose, launchWithAdmin, specificExecutable, launchWithTrainer, installation)` | Launch a game through the Game Handler |
| `isGameRunning(game)` | Whether a game is running |
| `checkTrainerExists(gameName, isCustom)` | Whether a trainer is installed in the game folder |
| `startSteam()` / `isSteamRunning()` | Steam client (for Online-Fix games) |

**Events**: `onGameLaunchSuccess((_, data) => …)`, `onGameLaunchError((_, data) => …)`, `onGameClosed((_, data) => …)`

---

## Downloads

| Method | Description |
|--------|-------------|
| `downloadFile(link, game, online, dlc, isVr, updateFlow, version, imgID, size, additionalDirIndex, gameID, browserDirect, browserAssetGameId, browserAssetLogoId)` | Start a download |
| `stopDownload(game, deleteContents, isKill)` | Pause, stop, or kill a download |
| `resumeDownload(game)` | Resume a stopped download |
| `retryDownload(link, game, online, dlc, version)` | Retry a failed download |
| `checkRetryExtract(game)` / `retryExtract(game)` | Retry extraction of the retained archive |
| `extractionRecoveryAction(game, requestId, action)` | Answer an extraction recovery prompt (for example, after antivirus removed files) |
| `runElevatedInstaller(installerPath)` / `completeManualInstall(game)` | Manual installer flow |
| `getDownloads()` | Active downloads and their `downloadingData` |
| `isDownloaderRunning()` | Whether any download is running |
| `getDownloadHistory()` | Download history |
| `getDownloadLog(lines)` | The last lines of `downloadmanager.log` |
| `downloadItem(url)` | Download a Steam Workshop item |
| `downloadSoundtrack(track, game)` | Download a soundtrack track |
| `downloadTrainerToGame(downloadUrl, gameName, isCustom)` | Install a trainer into a game folder |

**Events**: `onDownloadProgress(callback)` (Workshop Downloader log and progress), `onDownloadComplete(callback)`, `onDownloadError(callback)`

### Browser Downloads and Deep Links

| Method | Description |
|--------|-------------|
| `getPendingBrowserDownloads()` | Downloads sent by the extension that haven't been handled yet |
| `acknowledgeBrowserDownload(url)` | Mark a browser download as handled |

**Events**: `onProtocolDownloadUrl((_, url) => …)`, `onProtocolGameUrl((_, { gameID }) => …)`, `onInterceptedDownloadUrl((_, url) => …)`

---

## File & Directory Management

| Method | Description |
|--------|-------------|
| `openGameDirectory(game, isCustom, installation)` | Open a game's folder |
| `openDirectoryDialog()` / `openFolderDialog()` | Folder picker |
| `openFileDialog(exePath)` | File picker |
| `getPathForFile(file)` | Absolute path of a file dropped into the app (synchronous) |
| `canCreateFiles(directory)` | Check write permissions |
| `checkFileExists(filePath)` | Check whether a file exists |
| `getDriveSpace(path)` / `getDrives()` / `listDirectory(dirPath)` | Drives and folders |
| `getAssetPath(filename)` / `getAudioAsset(filename)` | Bundled images and sounds |
| `readLocalFile(filePath, encoding)` | Read a local file |
| `getTempPath()` / `writeFile(filePath, buffer)` / `deleteFile(filePath)` | Temporary files |

**Events**: `onDirectorySizeStatus(callback)`

---

## Tools & Dependencies

| Method | Description |
|--------|-------------|
| `getInstalledTools()` / `installTool(tool)` | Sidecars (`translator`, `torrent`, `ludusavi`) |
| `installDependencies()` / `checkGameDependencies()` | Windows game dependencies |
| `openReqPath(game)` | Open a game's required libraries folder |
| `installPython()` / `installWine()` | Python and Wine setup (Linux and macOS) |
| `isSteamCMDInstalled()` / `installSteamCMD()` | SteamCMD for the Workshop Downloader |
| `folderExclusion(enabled)` | Add or remove Windows Defender exclusions for the download folders |
| `isWatchdogRunning()` | Whether the Achievement Watcher is running |

**Events**: `onInstallProgress(callback)`, `onDependencyInstallationStatus((_, data) => …)`

---

## Linux Compatibility (Proton, UMU, Wine)

| Method | Description |
|--------|-------------|
| `getRunners()` / `detectProton()` / `resolveRunner(override)` | Detect and resolve runners |
| `selectCustomRunner()` | Pick a custom Proton or Wine runner |
| `downloadProtonGE()` / `getProtonGEInfo()` / `checkProtonGEUpdate()` / `cleanupOldProtonGE(keepVersion)` | Proton-GE |
| `downloadProtonCachyOS()` / `getProtonCachyOSInfo()` / `checkProtonCachyOSUpdate()` / `cleanupOldProtonCachyOS(keepVersion)` | Proton CachyOS |
| `isUmuInstalled()` / `downloadUmuLauncher()` | UMU-Launcher |
| `downloadUmuProton()` / `getUmuProtonInfo()` / `checkUmuProtonUpdate()` / `cleanupOldUmuProton(keepVersion)` | UMU-Proton |
| `umuRefreshDatabase()` / `umuFindId(gameName)` / `umuAutoDetect(gameName)` | UMU database lookups |
| `umuGetGameId(gameName)` / `umuSetGameId(gameName, umuId)` | A game's UMU ID |
| `deleteGamePrefix(gameName)` / `getPrefixSize(gameName)` / `openPrefixFolder(gameName)` | A game's Wine/Proton prefix |

---

## Ascendara Retro (`window.electron.retro`)

| Method | Description |
|--------|-------------|
| `getState()` | Retro library, profiles, and running jobs |
| `pick(kind)` | Pick an emulator, core, ROM folder, or save folder |
| `saveProfile(id, profile)` | Save a console setup |
| `scan(id)` | Scan a console's ROM folders |
| `updateCatalogue(source)` | Download or import the LaunchBox catalogue |
| `search(id, query)` | Search the catalogue |
| `updateGame(id, data)` / `setCover(id, file)` / `remove(id)` / `reveal(id)` | Edit, set a cover, remove, or show a game's files |
| `website(id, emulatorId)` | Open an emulator's website |
| `launch(id, disc)` | Launch a game |
| `backup(id, cloud)` / `restore(id, data)` / `recoveryFolder()` | Save backups |
| `cards(id)` / `cardAction(id, original, name, duplicate)` | List, rename, or duplicate memory cards |
| `onChanged(callback)` | Subscribe to library and job changes |

---

## Updates

| Method | Description |
|--------|-------------|
| `checkForUpdates()` | Check for an app update |
| `downloadUpdate()` / `updateAscendara()` | Download and install the update |
| `isUpdateDownloaded()` / `isBrokenVersion()` | Update state |
| `deleteInstaller()` | Delete the downloaded installer |
| `switchBranch(branch)` | Switch to `live`, `public-testing`, or `experimental` |
| `uninstallAscendara()` | Uninstall Ascendara (Windows) |

**Events**: `onUpdateAvailable`, `onUpdateReady`, `onUpdateDownloadProgress`, `onBranchSwitchProgress`, with `removeUpdateAvailableListener`, `removeUpdateReadyListener`, and `removeBranchSwitchProgressListener`

### Settings Recovery & Rollback (`window.electron.recovery`)

| Method | Description |
|--------|-------------|
| `createRecoveryPoint(reason, appVersion)` | Create a settings recovery point |
| `listRecoveryPoints()` / `restoreRecoveryPoint(id)` | List or restore recovery points |
| `listOfficialRollbackVersions()` | Previous official releases available for rollback (Windows, Live) |
| `rollbackAscendaraVersion(version)` | Install a previous official release |

---

## Themes & UI

| Method | Description |
|--------|-------------|
| `getBackgrounds()` / `setBackground(color, gradient)` | Backgrounds |
| `saveCustomThemeColors(customTheme)` | Save custom theme colors |
| `exportCustomTheme(customTheme)` / `importCustomTheme()` | Export or import a theme file |

---

## Discord RPC

| Method | Description |
|--------|-------------|
| `toggleDiscordRPC(enabled)` | Enable or disable Discord Rich Presence |
| `switchRPC(state)` | Change the Rich Presence state |

---

## Language & Translations

| Method | Description |
|--------|-------------|
| `downloadLanguage(langCode)` | Download a language file |
| `getLanguageFile(langCode)` / `saveLanguageFile(langCode, content)` | Read or save a language file |
| `getDownloadedLanguages()` / `languageFileExists(filename)` | Installed languages |
| `startTranslation(langCode)` / `cancelTranslation()` | Translate the UI into an extra language |

**Events**: `onTranslationProgress(callback)`

---

## System & Platform

| Method | Description |
|--------|-------------|
| `getPlatform()` | `win32`, `darwin`, or `linux` (synchronous) |
| `isOnWindows()` / `isOnLinux()` | Platform checks |
| `fetchSystemSpecs()` | CPU, GPU, RAM, and OS |
| `isDev()` | Whether Ascendara is running from source |
| `isExperiment()` / `getBranch()` / `getTestingVersion()` | Release branch information |
| `switchBuild(buildType)` | Legacy build switch |
| `showTestNotification()` | Show a test notification |

---

## API & Networking

| Method | Description |
|--------|-------------|
| `getAuthHeaders()` | Signed headers for protected Ascendara API requests |
| `getAPIKey()` | Deprecated; use `getAuthHeaders()` |
| `getAnalyticsKey()` / `getImageKey()` | Legacy keys (may be empty in source builds) |
| `openURL(url, options)` | Open an `http`/`https` URL in the browser |
| `fetchApiImage(endpoint, imgID, timestamp, signature)` | Fetch a game image from the API |
| `steamRequest(url)` | Steam request through the main process |
| `getHltbGameTimes(gameName)` | HowLongToBeat times |
| `requestAscendaraService(url, options)` / `request(url, options)` | Status checks against Ascendara service hosts (`GET`/`HEAD` on `/` only) |
| `generateWebappQRCode(code)` | QR code for [Ascendara Companion](/docs/features/companion) pairing |

**Events**: `onExternalWindowBlocked(callback)`

---

## Support, Profile, and Ascend Checkout

| Method | Description |
|--------|-------------|
| `uploadSupportLogs(sessionToken, appToken)` | Upload logs for a support case |
| `uploadProfileImage(imageBase64)` / `getProfileImage()` | Local profile picture |
| `getPendingCheckouts()` / `acknowledgeCheckout(sessionId)` | Ascend checkout sessions received through `ascendara://` |

**Events**: `onCheckoutSuccess(callback)`, `onCheckoutCanceled(callback)`

---

## qBittorrent (`window.qbittorrentApi`)

| Method | Description |
|--------|-------------|
| `login(credentials)` | Log in to the qBittorrent Web UI |
| `getVersion()` | Get the qBittorrent version (used to check the connection) |

---

## Legacy Low-Level IPC Access

Older renderer code uses `window.electron.ipcRenderer`, which is kept while those callers migrate:

```javascript
window.electron.ipcRenderer.invoke("channel-name", ...args);
window.electron.ipcRenderer.on("event-name", callback);
window.electron.ipcRenderer.off("event-name", callback);
window.electron.ipcRenderer.readFile(path, encoding);
window.electron.ipcRenderer.writeFile(path, content);
```

> **Warning**: Don't use the legacy bridge in new code. Add a named method to `preload.js` instead. Setting the strict legacy-IPC environment flag makes legacy calls throw, which helps find remaining callers.
