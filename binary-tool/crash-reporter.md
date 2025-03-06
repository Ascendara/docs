# Ascendara Crash Reporter

## Overview
The Crash Reporter provides error reporting and diagnostic capabilities across all Ascendara components. It features a modern, user-friendly interface with different display modes depending on the severity and source of the error.

## Features
- Comprehensive error code system organized by component
- User-friendly interface with modern styling
- Technical detail collection and display
- Report uploading capability
- Recovery suggestions and troubleshooting guidance
- Different UI modes for critical vs. utility errors
- Support for opening Discord community help
- Responsive window sizing based on error type

## Usage

### Command Line Arguments
```bash
AscendaraCrashReporter.exe [tool_id] [error_code] [error_message]
```

### Parameters
- `tool_id`
 Identifier of the affected tool
- `error_code`
 Numeric error code
- `error_message`
 Error description

### Example
```bash
AscendaraCrashReporter.exe "maindownloader" 1400 "Failed to initialize download"
```

## Development

### Tool Identifiers
```python
class AscendaraTool:
    GOFILE_HELPER = "gofilehelper"
    MAIN_DOWNLOADER = "maindownloader"
    GAME_HANDLER = "gamehandler"
    TOP_LEVEL = "toplevel"
    LANGUAGE_TRANSLATION = "languagetranslation"
    TORRENT_HANDLER = "torrenthandler"
    NOTIFICATION_HELPER = "notificationhelper"
```

### Error Code System

#### General Errors (1000-1004)
```python
class ErrorCodes:
    UNKNOWN_ERROR = 1000
    UNHANDLED_EXCEPTION = 1001
    UNHANDLED_REJECT = 1002
    NETWORK_ERROR = 1003
    INVALID_DATA = 1004
```

#### Game Handler Errors (1100-1199)
```python
    GAME_NOT_FOUND = 1100
    GAME_LAUNCH_ERROR = 1101
    GAME_CONFIG_ERROR = 1102
    GAME_PROCESS_ERROR = 1103
    SETTINGS_ERROR = 1104
    DOWNLOAD_DIR_ERROR = 1105
```

#### Language Translation Errors (1200-1299)
```python
    TRANSLATION_API_ERROR = 1200
    TRANSLATION_RATE_LIMIT = 1201
    TRANSLATION_FILE_ERROR = 1202
```

#### GoFile Helper Errors (1300-1399)
```python
    GOFILE_API_ERROR = 1300
    GOFILE_UPLOAD_ERROR = 1301
    GOFILE_DOWNLOAD_ERROR = 1302
    GOFILE_AUTH_ERROR = 1303
    GOFILE_RATE_LIMIT = 1304
    GOFILE_FILE_ERROR = 1305
```

#### Main Downloader Errors (1400-1499)
```python
    DOWNLOAD_INIT_ERROR = 1400
    DOWNLOAD_PROGRESS_ERROR = 1401
    DOWNLOAD_CANCEL_ERROR = 1402
    DOWNLOAD_VERIFY_ERROR = 1403
    DOWNLOAD_EXTRACT_ERROR = 1404
    DOWNLOAD_CLEANUP_ERROR = 1405
    SETTINGS_FILE_ERROR = 1406
    GAMES_FILE_ERROR = 1407
    HELPER_LAUNCH_ERROR = 1408
```

#### Torrent Handler Errors (1500-1599)
```python
    TORRENT_API_ERROR = 1500
    TORRENT_ADD_ERROR = 1501
    TORRENT_REMOVE_ERROR = 1502
    TORRENT_STATUS_ERROR = 1503
    TORRENT_CONFIG_ERROR = 1504
    TORRENT_FILE_ERROR = 1505
    TORRENT_INSTALL_ERROR = 1506
```

#### Notification Helper Errors (1600-1699)
```python
    NOTIFICATION_INIT_ERROR = 1600
    NOTIFICATION_DISPLAY_ERROR = 1601
    NOTIFICATION_THEME_ERROR = 1602
    NOTIFICATION_RESOURCE_ERROR = 1603
    NOTIFICATION_ANIMATION_ERROR = 1604
```

### UI Components
The crash reporter uses tkinter with modern styling for its UI, featuring two distinct modes:

#### Critical Error UI
```python
def create_critical_error_widgets(self):
    # Critical error header with warning icon
    error_header = ttk.Label(
        header_frame,
        text="⚠️ Critical Error: Ascendara Has Stopped Working",
        style='Critical.Header.TLabel'
    )
    
    # Error information in styled card
    error_card = ttk.Frame(self.critical_frame, style='Critical.Card.TFrame', padding=15)
    
    # Recovery suggestions with bullet points
    recovery_header = ttk.Label(
        recovery_frame,
        text="What You Can Try:",
        style='Critical.TLabel',
        font=('Segoe UI', 11, 'bold')
    )
```

#### Normal Error UI
```python
def create_normal_error_widgets(self):
    # Error header with warning icon
    error_header = ttk.Label(
        header_frame,
        text="⚠️ Ascendara Core Utility Crash",
        style='Header.TLabel'
    )
    
    # Tool identifier with context
    tool_prefix = ttk.Label(
        tool_frame,
        text="Affected Component: ",
        style='TLabel'
    )
    
    # Error information in styled card
    error_card = ttk.Frame(self.normal_frame, style='Card.TFrame', padding=12)
```

### Integration
To integrate crash reporting in your tool:
```python
def launch_crash_reporter(error_code, error_message):
    crash_reporter_path = os.path.join('./AscendaraCrashReporter.exe')
    subprocess.Popen([
        crash_reporter_path,
        "your_tool_id",
        str(error_code),
        error_message
    ], creationflags=subprocess.CREATE_NO_WINDOW)
```