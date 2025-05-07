# Ascendara Achievement Watcher

## Overview
Ascendara Achievement Watcher monitors and notifies users of game achievements in real time on the Ascendara platform. Built in JavaScript and optimized for Windows, it is based on [achievement-watcher](https://github.com/xan105/achievement-watcher) by xan105, but is heavily modified for seamless Ascendara integration.

## Modifications from Upstream
Compared to the original achievement-watcher, the Ascendara version:
- **Removes the original notification system**
- **Integrates with Ascendara's notification helper** for a unified user experience
- **Streamlines the codebase** to focus on core achievement tracking

## Key Features
- Real-time monitoring of achievement/stat files
- Rich, customizable notifications for unlocked achievements
- Single-instance enforcement to prevent duplicate processes
- Full support for both standard and custom Ascendara games
- Automatic updates of achievement state files
- Spam protection to avoid duplicate notifications
- Process-aware: notifies only if the game is running
- Multi-language support via Steam API code mapping
- Robust error handling and detailed logging

## Usage
Achievement Watcher runs as a background process, typically started by Ascendara. There is no standalone CLI; all activity is logged to the console and status files for platform integration.

### Workflow
1. **Initialization:**
   - Ensures only one instance runs
   - Loads settings and environment variables
2. **Folder Monitoring:**
   - Uses `node-watch` to monitor configured directories/files
3. **File Change Handling:**
   - Parses achievement data on updates
   - Applies spam protection
   - Verifies the game is running
4. **Notification:**
   - Triggers notifications via `AscendaraNotificationHelper.exe`
   - Updates achievement cache and state files
5. **State Update:**
   - Writes to `achievements.ascendara.json` (standard games) or `achievementWater` in `games.json` (custom games)

## Technical Notes
- **Language:** Maps user language to Steam API codes using `steam.json`
- **Configuration:** Reads from `ascendarasettings.json` and environment variables
- **Persistence:** Caches achievement state for all games
- **Error Handling:** Logs errors and warnings, fails gracefully
- **Dependencies:** `single-instance`, `node-watch`, `moment`, and standard Node.js modules

## Example Notification Flow
1. User unlocks an achievement
2. Watcher detects the file change
3. If new and not previously notified, triggers a notification
4. Updates and persists achievement state

## Credits
The Ascendara Achievement Watcher is a stripped down version of [Achievement Watcher](https://github.com/xan105/achievement-watcher) by xan105, and more specifically the [watchdog](https://github.com/xan105/Achievement-Watcher/tree/1.x/service/watchdog) used by the Achievement Watcher project. 