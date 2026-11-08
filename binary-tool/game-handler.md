# Ascendara Game Handler

## Overview
The Game Handler launches games, tracks playtime and launch counts, and handles everything that happens while a game runs: Discord Rich Presence for shortcut launches, FLiNG trainers, Linux compatibility runners, and Ludusavi save backups when the game closes. Ascendara starts it every time you click **Play**, and desktop shortcuts start it directly.

Source: `binaries/AscendaraGameHandler/src/` (Rust, entry point `main.rs`). The handler was rewritten from Python to Rust; it keeps the same arguments, files, and log output, so the app and existing shortcuts work unchanged.

## Features
- Launches Windows executables, native Linux and macOS executables, and macOS `.app` bundles
- Runs Windows games on Linux through UMU-Launcher, Proton, or Wine, and on macOS through Highball (Apple Silicon) or Wine
- Admin launches on Windows (`runas`)
- Custom launch options
- Playtime and launch count tracking, including games that start through a launcher or bootstrapper
- FLiNG trainer launching and cleanup
- Ludusavi save backup after the game closes
- Discord Rich Presence for shortcut launches
- Crash reporter integration

## Usage

### Command Line Arguments
```bash
AscendaraGameHandler.exe <game_path> <is_custom_game> <admin> [--shortcut] [--ludusavi] [--trainer] [--gameLaunchCmd "<options>"] [runner options]
```

### Parameters
| Argument | Description |
| --- | --- |
| `game_path` | Path to the game executable (or `.app` on macOS) |
| `is_custom_game` | `true`/`1` for custom or imported games stored in `games.json` |
| `admin` | `true`/`1` to launch with administrator privileges (Windows) |
| `--shortcut` | Launched from a desktop shortcut. The handler sets Discord Rich Presence itself, because Ascendara isn't managing the launch. |
| `--ludusavi` | Run a Ludusavi backup after the game closes |
| `--trainer` | Launch `ascendaraFlingTrainer.exe` from the game folder alongside the game |
| `--gameLaunchCmd "<options>"` | Launch options passed to the game |

### Runner Options
On Linux and macOS, Ascendara passes the runner it resolved for the game. The options keep their `--linux-` prefix on macOS too:

| Argument | Description |
| --- | --- |
| `--linux-runner-type` | `umu`, `proton`, `wine`, or `highball` |
| `--linux-runner-path` | Path to `umu-run`, the Proton folder, the Wine binary, or the bundled `highball` CLI |
| `--linux-compat-data` | The game's compatibility data folder (`~/.ascendara/compatdata/<game>` on Linux, the Highball bottle on macOS) |
| `--linux-steam-path` | The Steam installation, used by Proton |
| `--linux-umu-id` | The game's UMU ID (defaults to `umu-default`) |
| `--linux-proton-path` | The Proton build UMU should use (`PROTONPATH`). If omitted, UMU uses UMU-Proton. |
| `--linux-umu-use-steam` | `1` to let UMU use the Steam installation |
| `--linux-steamappid` | Online-Fix games: sets `SteamAppId` and `SteamGameId`, and injects the Steam overlay with Proton |
| `--linux-winedlloverrides` | Online-Fix games: DLL overrides merged into `WINEDLLOVERRIDES` |
| `--highball-bottle` | macOS: the Highball bottle (environment) to run the game in, shared or per game |
| `--highball-home` | macOS: Ascendara's Highball home, passed as `HIGHBALL_HOME` |
| `--highball-renderer` | macOS: the graphics translator (`dxmt`, `d3dmetal`, `dxvk`, `vkd3d`, or `wined3d`) |

### Examples
```bash
# Basic launch
AscendaraGameHandler.exe "C:/Games/MyGame/game.exe" false false

# Shortcut launch with a backup when the game closes
AscendaraGameHandler.exe "C:/Games/MyGame/game.exe" false false --shortcut --ludusavi

# Launch as administrator with a trainer and launch options
AscendaraGameHandler.exe "C:/Games/MyGame/game.exe" false true --trainer --gameLaunchCmd "-windowed -nointro"

# Linux with UMU-Launcher
./AscendaraGameHandler "/games/MyGame/game.exe" false false \
  --linux-runner-type umu --linux-runner-path ~/.ascendara/tools/umu/umu-run \
  --linux-compat-data ~/.ascendara/compatdata/MyGame --linux-umu-id umu-12345

# macOS (Apple Silicon) with Highball
./AscendaraGameHandler "/Games/MyGame/game.exe" false false \
  --linux-runner-type highball --linux-runner-path /Applications/Ascendara.app/Contents/Resources/Highball/highball \
  --highball-home ~/Library/Application\ Support/ascendara/highball --highball-bottle ascendara --highball-renderer dxmt
```

## Building

```bash
cd binaries/AscendaraGameHandler
cargo build --release --locked
```

The executable is written to `target/release/AscendaraGameHandler(.exe)`, where `package.json` and development mode expect it. See [Creating the Tools](/docs/developer/creating-the-tools).

## How a Launch Works

1. **Find the game's data.** For a downloaded game, the handler walks up from the executable's folder until it finds `{folder}.ascendara.json`, which identifies the game. For a custom game, it reads `games.json` from the download directory and finds the entry with the matching executable.
2. **Check the executable.** If it doesn't exist, the handler writes `runError` to the game's JSON and exits.
3. **Update the game's state.** It increments `launchCount`, sets `isRunning` to `true`, and adds the game to `runningGames` in the settings file.
4. **Launch the game** with the right method for the platform (see below).
5. **Launch the trainer** if `--trainer` was passed.
6. **Track playtime** while the game runs.
7. **Clean up** when the game closes: stop the trainer, remove the game from `runningGames`, set `isRunning` to `false`, run the Ludusavi backup, and clear Discord Rich Presence.

All JSON files are written atomically (to a temporary file that replaces the original) so a crash or power loss can't corrupt them.

### Launch Methods

| Platform | Executable | Method |
| --- | --- | --- |
| Windows | `.exe` | Started directly, or with `ShellExecuteW` and the `runas` verb when `admin` is set |
| Linux | `.exe` with `--linux-*` options | `umu-run`, `proton run`, or Wine with an isolated prefix |
| macOS (Apple Silicon) | `.exe` with `highball` runner | `highball run [--renderer <r>] <bottle> <exe> -- <args>` |
| Linux / macOS | `.exe` without runner options | System Wine with a prefix in `~/.ascendara/compatdata/<game>` |
| macOS | `.app` | The bundle's `CFBundleExecutable` from `Contents/MacOS` |
| Linux / macOS | Native executable | Started directly |

With UMU-Launcher, the handler sets `GAMEID`, `WINEPREFIX`, `STEAM_COMPAT_DATA_PATH`, and optionally `PROTONPATH`, and installs the x86 and x64 Visual C++ redistributables into the prefix through `umu-run`.

### Launch Options
On Windows, launch options are appended to the executable's command line. With UMU, Proton, or Wine, tokens in the form `NAME=value` become environment variables, `%command%` is dropped, and the remaining tokens are passed as arguments, so Steam-style launch options such as `PROTON_LOG=1 %command% -windowed` work.

### Playtime Tracking
The handler adds 180 seconds to `playTime` every 3 minutes while the game runs, and adds the remaining seconds when the game closes, so short sessions are counted too. `playTime` is stored in seconds in `{game}.ascendara.json`, or in the game's entry in `games.json` for custom games.

Some games start through a launcher, bootstrapper, or anti-cheat wrapper that exits while the real game keeps running. When the process the handler started exits, it checks for other processes running from the game's install folder and keeps tracking until all of them have exited.

### Trainers
With `--trainer`, the handler looks for `ascendaraFlingTrainer.exe` in the game's root folder (the folder containing `.ascendara.json`, or the executable's folder for custom games). If Windows reports that the trainer needs elevation, it relaunches the trainer as administrator. On Linux and macOS, the trainer runs through the same runner as the game. The trainer is terminated when the game closes. With Highball, every Wine process in the game's bottle is stopped when the game closes.

### Ludusavi Backups
With `--ludusavi`, the handler runs a Ludusavi backup after the game closes, using the backup settings from `ascendarasettings.json`:

```bash
ludusavi --config <config dir> [--no-manifest-update] backup "<game name>" \
  --path <backupLocation> --format <zip|simple> --full-limit <backupsToKeep> \
  --compression <level> --force [--wine-prefix <prefix>]
```

The backup only runs when backups are enabled in Ascendara's settings. On Linux, if the game has no custom save paths, the handler passes the game's Wine prefix with `--wine-prefix`. On macOS, it passes the game's Highball bottle, which is a Wine prefix.

## Files

### Settings
The handler reads `ascendarasettings.json` for the download directory, Ludusavi options, and `runningGames`:

| Platform | Path |
| --- | --- |
| Windows | `%APPDATA%\ascendara\ascendarasettings.json` |
| macOS | `~/Library/Application Support/ascendara/ascendarasettings.json` |
| Linux | `~/.config/ascendara/ascendarasettings.json` |

### Game State
```json
{
  "game": "MyGame",
  "isRunning": true,
  "launchCount": 5,
  "playTime": 7200
}
```

```json
{
  "runningGames": {
    "MyGame": "C:/Games/MyGame/game.exe"
  }
}
```

### Log File

| Platform | Path |
| --- | --- |
| Windows | `%APPDATA%\Ascendara by tagoWorks\gamehandler.log` |
| macOS | `~/Library/Application Support/ascendara/gamehandler.log` |
| Linux | `~/.ascendara/gamehandler.log` |

Output from UMU, Proton, Wine, and Highball is also written to this log.

## Error Handling
If the launch fails, the handler sets `isRunning` back to `false`, decrements `launchCount`, and registers the [Crash Reporter](/docs/binary-tool/crash-reporter) to open when it exits:

```bash
AscendaraCrashReporter gamehandler 1 "<error message>"
```

Launch problems that aren't crashes, such as a missing executable or a failed runner, are written to `runError` in the game's JSON so Ascendara can show them.

## Discord Rich Presence
For shortcut launches (`--shortcut`), the handler connects to Discord and shows "Playing a Game" with the game's name and a **Play on Ascendara** button, then clears the presence when the game closes. For launches from Ascendara, the app's own Discord RPC module updates the presence instead.
