# Ascendara Notification Helper
## Overview
The Notification Helper is a PyQt6-based tool that displays modern, animated notifications with customizable themes, titles, and messages. It features smooth fade animations, theme support, and automatic cleanup.

## Features
- Smooth fade in/out animations with cubic easing curves
- Automatic cleanup after display (4 second display time)
- Crash reporter integration via atexit
- High DPI support with proper scaling
- Customizable titles and messages
- Always-on-top display with frameless window
- Elegant rounded corners and borders
- Comprehensive logging system
- Multiple theme options (12 different color schemes)
- Cached icon loading for performance

## Usage
```bash
AscendaraNotificationHelper.exe --theme <theme> --title <title> --message <message>
```

### Parameters
- `--theme`: (Optional) The visual theme to use. Options include: light, dark, blue, purple, emerald, rose, cyberpunk, sunset, forest, midnight, amber, ocean. Default is 'dark'
- `--title`: (Optional) The notification title. Default is 'Notification'
- `--message`: (Optional) The notification message. Default is 'This is a notification'

### Example
```bash
AscendaraNotificationHelper.exe --theme emerald --title "Update Available" --message "A new version of Ascendara is ready to install"
```

## Technical Details
- Built with PyQt6 for modern UI components
- Uses hardware acceleration for smooth animations
- Implements automatic garbage collection
- Integrates with Ascendara's crash reporting system
- Supports high DPI displays and scaling
- Loads and caches application icon for performance
- Responsive design with proper padding and spacing
- Fallback icon mechanism when icon file is unavailable
- Optimized animation durations (200ms) for snappy performance

## Error Handling
The tool integrates with Ascendara's crash reporting system. If an error occurs, it will automatically launch the crash reporter with relevant error details via the atexit module.

## Integration
To integrate the Notification Helper into your Ascendara application:

```python
import subprocess

def show_notification(theme, title, message):
    subprocess.Popen([
        "AscendaraNotificationHelper.exe",
        "--theme", theme,
        "--title", title,
        "--message", message
    ], creationflags=subprocess.CREATE_NO_WINDOW)
```