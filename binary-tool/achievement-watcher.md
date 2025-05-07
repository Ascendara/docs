# Ascendara Achievement Watcher

## Overview
Ascendara Achievement Watcher is a real-time achievement monitoring and notification tool for games on the Ascendara platform. It is based on [achievement-watcher](https://github.com/xan105/achievement-watcher) by xan105, but has been significantly modified for seamless integration with Ascendara. Unlike other tools in this suite, it is written in JavaScript and optimized for Windows environments.

Achievement Watcher listens for changes in achievement data files, updates local achievement state, and triggers rich notifications when new achievements are unlocked. It supports both standard Ascendara-installed games and custom games, with robust error handling and single-instance protection.

## Modifications from Upstream
The Ascendara version of Achievement Watcher introduces several important changes from the original [achievement-watcher](https://github.com/xan105/achievement-watcher):

- **Removed the original notification handling system**
- **Integrated with Ascendara's own notification helper** for a consistent user experience
- **Streamlined the codebase** to focus on core achievement tracking functionality

## Key Features
- **Real-time monitoring** of achievement files for supported games
- **Rich notifications** for unlocked achievements (customizable theme, icons, and details)
- **Single-instance enforcement** to prevent duplicate processes
- **Supports both standard and custom games** (integrates with Ascendara's game and settings JSONs)
- **Automatic update** of achievements state files for running games
- **Spam protection** to avoid duplicate notifications
- **Process-aware**: Only notifies if the game is running (with multiple detection strategies)
- **Multi-language support** (maps user language to Steam API codes)
- **Robust error handling** and detailed logging

## Usage

Achievement Watcher runs as a background process, typically started automatically by Ascendara. It does not have a standalone CLI, but logs activity to the console and updates status files for integration with the rest of the platform.

### Workflow
1. **Initialization:**
   - Acquires a single-instance lock to prevent multiple watchdogs.
   - Loads user and environment settings (paths, language, API keys).
2. **Folder Monitoring:**
   - Uses `node-watch` to recursively monitor achievement/stat files in configured directories.
   - Watches only files explicitly listed in game configuration.
3. **File Change Handling:**
   - On file update, parses achievement data and checks for newly unlocked achievements.
   - Applies spam protection to avoid rapid duplicate notifications.
   - Verifies the game is running (checks process list, fullscreen state, or disables check per config).
4. **Notification:**
   - Triggers a notification via `AscendaraNotificationHelper.exe` with achievement details (title, icon, description, etc).
   - Updates the local achievement cache and state files for persistence.
5. **State Update:**
   - Updates `achievements.ascendara.json` for standard games, or the `achievementWater` key in `games.json` for custom games.

## Technical Notes
- **Language Support:**
  - Automatically maps user language to Steam API codes using `steam.json`.
- **Configuration:**
  - Reads settings from `ascendarasettings.json` in the user's Electron app data.
  - Uses environment variables for paths and API keys.
- **Persistence:**
  - Local achievement state is cached and written to disk for both standard and custom games.
- **Error Handling:**
  - Logs all errors and warnings to the console.
  - Fails gracefully if files or directories are missing or invalid.
- **Dependencies:**
  - `single-instance`, `node-watch`, `moment`, and other Node.js libraries.

## Example Notification Flow
1. User unlocks an achievement in a running game.
2. Achievement Watcher detects the file change and parses the new achievement.
3. If the achievement is new and not previously notified, a notification is triggered.
4. The achievement state is updated and persisted for the running game.

## Credits
- Based on [achievement-watcher](https://github.com/xan105/achievement-watcher) by xan105, with extensive modifications for Ascendara.
- JavaScript implementation by the Ascendara team.
