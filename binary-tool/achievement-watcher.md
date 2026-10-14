# Ascendara Achievement Watcher

## Overview
The Achievement Watcher detects achievements you unlock in supported games and shows a notification in real time. It's written in JavaScript and runs in the background while Ascendara is open. It's based on the watchdog service from [Achievement Watcher](https://github.com/xan105/achievement-watcher) by xan105, heavily modified for Ascendara.

Source: `binaries/AscendaraAchievementWatcher/src/` (`watchdog.js` is the entry point)

## Modifications from Upstream
Compared to the original Achievement Watcher, the Ascendara version:
- **Removes the original notification system**
- **Uses Ascendara's [Notification Helper](/docs/binary-tool/notification-helper)** for achievement pop-ups
- **Writes achievements into Ascendara's game data** so they appear on the game page
- **Watches Ascendara's Wine and Proton prefixes** on Linux and macOS
- **Streamlines the codebase** to focus on achievement tracking

> **Note:** The standalone Achievement Watcher app and Ascendara's watcher use the same achievement system and can't run together. If Ascendara shows **Achievement Service Not Running**, uninstall Achievement Watcher and restart Ascendara.

## Key Features
- Real-time monitoring of achievement and stat files
- Notifications for newly unlocked achievements, with the achievement's icon
- Single-instance enforcement to prevent duplicate processes
- Support for downloaded and custom games
- Spam protection so the same achievement isn't announced twice
- Achievement names in your Ascendara language, using Steam's language codes
- Resource limits: at most 50 file watchers and 20 cached game schemas

## Usage
Ascendara starts the watcher when it launches. There is no standalone CLI. Activity is logged to the console, and the watcher records its status in `timestamp.ascendara.json` (`watchdogRunning` and `watchdogLastUpdated`), which Ascendara reads to check that the service is running.

### Watched Folders
On Windows, the watcher monitors the default save locations used by common Steam emulators, including:

- `%PUBLIC%\Documents\Steam\CODEX`, `%APPDATA%\Steam\CODEX`
- `%APPDATA%\Goldberg SteamEmu Saves`, `%APPDATA%\GSE Saves`
- `%APPDATA%\EMPRESS`, `%PUBLIC%\Documents\EMPRESS`
- `%PUBLIC%\Documents\Steam\RUNE`
- `%PUBLIC%\Documents\OnlineFix`
- `%PROGRAMDATA%\Steam`

On Linux and macOS, it monitors the same locations inside each game's prefix in `~/.ascendara/compatdata`.

You can add more folders in **Settings** → **Achievement Watcher**. They're stored in the `watchingFolders` setting. The watcher also monitors game folders that already have an `achievements.ascendara.json`.

### Workflow
1. **Initialization**: Makes sure only one instance runs and loads settings from `ascendarasettings.json`.
2. **Folder monitoring**: Watches the folders above with `node-watch`, debouncing file events.
3. **File change handling**: Parses the changed achievement file, looks up the game's achievement schema, and compares it with the cached state.
4. **Notification**: For each new achievement, starts the Notification Helper with `--is-achievement` and your theme.
5. **State update**: Writes the achievements to the running game's data.

### Where Achievements Are Stored
- **Downloaded games**: `achievements.ascendara.json` in the game's folder
- **Custom games**: The game's entry in `games.json` in the download directory

The watcher finds the game that's currently running (`isRunning: true`) to decide where to write.

## Technical Notes
- **Language**: Maps your Ascendara language to Steam API language codes using `steam.json`
- **Configuration**: Reads `ascendarasettings.json` from `%APPDATA%\ascendara\`, `~/Library/Application Support/ascendara/`, or `~/.config/ascendara/`
- **Persistence**: Caches achievement state per game
- **Error handling**: Logs errors and warnings and keeps running
- **Dependencies**: `single-instance`, `node-watch`, `moment`, `ini`, `regedit`, `find-up`, and standard Node.js modules

## Building
The watcher is packaged with `pkg` on a Node 18 runtime:

```bash
cd binaries/AscendaraAchievementWatcher
yarn install --frozen-lockfile
yarn build-win      # or build-linux / build-mac
```

The output is `dist/AscendaraAchievementWatcher(.exe)`.

## Credits
The Ascendara Achievement Watcher is a stripped-down version of [Achievement Watcher](https://github.com/xan105/achievement-watcher) by xan105, and more specifically of the [watchdog](https://github.com/xan105/Achievement-Watcher/tree/1.x/service/watchdog) service used by that project.
