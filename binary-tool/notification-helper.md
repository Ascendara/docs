# Ascendara Notification Helper

## Overview
The Notification Helper is a PyQt6 tool that shows Ascendara's desktop notifications: download and index updates from the app and helper tools, and achievement pop-ups from the [Achievement Watcher](/docs/binary-tool/achievement-watcher). Notifications match your Ascendara theme, animate in and out, and close themselves after a few seconds.

Source: `binaries/AscendaraNotificationHelper/src/AscendaraNotificationHelper.py`

## Features
- Two layouts: standard notifications and achievement notifications
- 12 color themes
- Fade and slide animations
- Always on top, frameless, and doesn't take focus from your game
- **Open** and **Dismiss** buttons on standard notifications
- Closes automatically after about 4–5 seconds
- High-DPI support
- Crash Reporter integration

## Usage
```bash
AscendaraNotificationHelper --theme <theme> --title "<title>" --message "<message>"
AscendaraNotificationHelper --is-achievement --theme <theme> --game "<game>" --message "<achievement>" [--icon <url>] [--appid <id>]
```

### Parameters
| Argument | Default | Description |
| --- | --- | --- |
| `--theme` | `dark` | `light`, `dark`, `blue`, `purple`, `emerald`, `rose`, `cyberpunk`, `sunset`, `forest`, `midnight`, `amber`, or `ocean`. Unknown themes fall back to `dark`. |
| `--title` | `Notification` | The title of a standard notification |
| `--message` | `This is a notification` | The message, or the achievement name in achievement mode |
| `--is-achievement` | Off | Show the achievement layout |
| `--game` | — | The game name shown on an achievement notification |
| `--icon` | — | URL of the achievement icon |
| `--appid` | — | The game's Steam App ID |
| `--achievement`, `--description` | — | Accepted for compatibility; the achievement name comes from `--message` |

### Examples
```bash
AscendaraNotificationHelper.exe --theme emerald --title "Download Complete" --message "Your game is ready to play"

AscendaraNotificationHelper.exe --is-achievement --theme dark --game "Example Game" --message "First Steps" --icon "https://example.com/icon.jpg"
```

## Who Calls It
- **Ascendara** (main process) for desktop notifications, when **Desktop Notifications** is on in Settings
- **Downloader** and **Torrent Handler**, when started with `--withNotification <theme>`
- **Achievement Watcher**, for unlocked achievements, using the theme from your settings

## Logs
The helper logs to `notificationhelper.log`:

| Platform | Path |
| --- | --- |
| Windows | `%APPDATA%\Ascendara by tagoWorks\notificationhelper.log` |
| macOS / Linux | `~/.config/Ascendara by tagoWorks/notificationhelper.log` |

## Error Handling
If the helper fails to show a notification, it registers the [Crash Reporter](/docs/binary-tool/crash-reporter) to open on exit with the `notificationhelper` tool ID.

## Integration
```python
import subprocess, sys

def show_notification(helper_path, theme, title, message):
    kwargs = {"creationflags": subprocess.CREATE_NO_WINDOW} if sys.platform == "win32" else {}
    subprocess.Popen(
        [helper_path, "--theme", theme, "--title", title, "--message", message],
        **kwargs,
    )
```
