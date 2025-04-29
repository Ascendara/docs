# Ascendara Downloader

## Overview
The Ascendara Downloader is a robust, high-performance, multi-threaded tool for downloading, extracting, and verifying game files. It is designed for reliability, efficiency, and seamless integration with the Ascendara ecosystem, featuring advanced error handling, detailed logging, and smart file management.

## Key Features
- **Multi-threaded downloads** with SmartDL for fast, reliable transfers
- **Automatic resume** and smart retry logic for interrupted downloads
- **Configurable speed limits and thread counts** (reads from Electron settings)
- **Real-time progress tracking** (progress %, speed, ETA)
- **Automatic archive extraction** (.zip, .rar) with CommonRedist cleanup
- **File verification** after extraction (size checks, filemap)
- **Comprehensive error handling** with crash reporter integration
- **Safe, atomic JSON writes** for state tracking and error recovery
- **Detailed logging** to a dedicated log file and console
- **Automatic file flattening** (removes nested directories post-extraction)
- **Memory-efficient file processing**

## CLI Usage

```bash
AscendaraDownloader.exe [url] [game] [online] [dlc] [isVr] [updateFlow] [version] [size] [download_dir] [--withNotification THEME]
```

### Arguments
- `url` - Download URL for the game file
- `game` - Name of the game
- `online` - Is the game online? (`true`/`false`)
- `dlc` - Is DLC included? (`true`/`false`)
- `isVr` - Is this a VR game? (`true`/`false`)
- `updateFlow` - Is this an update? (`true`/`false`)
- `version` - Game version string
- `size` - Expected file size (e.g., `12 GB`, `439 MB`)
- `download_dir` - Directory to save the downloaded files
- `--withNotification` - Optional theme for notifications (light, dark, blue)

## Workflow

### 1. Initialization
- Creates a dedicated download directory per game
- Initializes or updates a `.ascendara.json` file for tracking download state
- Sets up detailed logging to a persistent log file (`downloadmanager.log`)

### 2. Download Process
- Determines final file name from URL or HTTP headers
- Reads user settings for speed/thread limits if available
- Starts a SmartDL download with real-time progress updates:
    - Progress %, speed, and ETA are written to the JSON state file
    - Handles network errors, SSL issues, and provider blocks gracefully
- On completion, detects file type (zip, rar, exe, etc.) and renames if needed

### 3. Extraction & File Management
- Automatically extracts `.zip` or `.rar` archives
- Cleans up `_CommonRedist` folders and `.url` files
- Flattens nested directories (moves files up one level if needed)
- Builds a `filemap.ascendara.json` for verification

### 4. Verification
- Verifies all extracted files by size
- Reports missing or mismatched files in the JSON state
- Cleans up download state after verification

## Error Handling & Logging
- All errors are logged to `downloadmanager.log` and the console
- On fatal errors, launches the Ascendara Crash Reporter with error details
- Uses atomic, retry-safe JSON writes to prevent data loss or corruption
- Handles permission errors and writes backups if necessary

## Example State File (`.ascendara.json`)
```json
{
  "game": "GameName",
  "online": true,
  "dlc": false,
  "isVr": false,
  "version": "1.0.0",
  "size": "12 GB",
  "executable": "C:/Games/GameName/GameName.exe",
  "isRunning": false,
  "downloadingData": {
    "downloading": true,
    "verifying": false,
    "extracting": false,
    "updating": false,
    "progressCompleted": "37.50",
    "progressDownloadSpeeds": "4.20 MB/s",
    "timeUntilComplete": "2m 13s"
  }
}
```

## Settings
- Reads download speed and thread count from `ascendarasettings.json` (if present in Electron's APPDATA)
- Example settings:
```json
{
  "downloadLimit": 5000,   // in KB/s
  "threadCount": 8
}
```

## Crash Reporter
- On any unhandled error, the downloader launches `AscendaraCrashReporter.exe` with error details for user-friendly reporting

## References
- [Download Manager Tool Documentation](https://ascendara.app/docs/binary-tool/downloader)

---

For developer details, see the full Python source in the Ascendara repository.

## How It Works

The Ascendara Downloader is designed for robust, high-performance downloads and seamless user experience. Below is a detailed breakdown of its internal architecture and advanced features, modeled after the clarity of the GoFile Helper documentation.

### Key Components

#### Progress Tracking
Real-time progress updates are written to a dedicated JSON state file:
```python
self.game_info = {
    "game": game,
    "online": online,
    "dlc": dlc,
    "isVr": isVr,
    "version": version,
    "size": size,
    "executable": executable_path,
    "isRunning": False,
    "downloadingData": {
        "downloading": True,
        "verifying": False,
        "extracting": False,
        "updating": False,
        "progressCompleted": "0.00",
        "progressDownloadSpeeds": "0.00 KB/s",
        "timeUntilComplete": "calculating..."
    }
}
```
- Updates every 0.5 seconds with progress %, speed, and ETA
- Atomic, retry-safe writes for data integrity

#### Download Management
Handles robust, multi-threaded downloads with SmartDL:
```python
def download(self, url, ...):
    # Determines filename and destination
    # Reads user settings for speed/thread limits
    # Starts SmartDL download with real-time progress callbacks
    # Handles network errors, SSL issues, and provider blocks
    # Updates state file throughout process
```
- Smart resume and retry logic for network issues
- Detects file type and renames if needed for extraction

#### Archive Extraction & Verification
Automates extraction and ensures file integrity:
```python
def _extract_files(self, ...):
    # Extracts .zip/.rar files
    # Cleans up CommonRedist and .url files
    # Flattens nested directories
    # Builds filemap for verification
    self._verify_extracted_files(watching_path)
```
- Verifies all files by size and reports issues in the state file
- Removes unnecessary files/folders for clean installs

#### Error Handling & Logging
Comprehensive error handling with crash reporting:
```python
def handleerror(game_info, game_info_path, e):
    # Updates state with error info and persists safely
    safe_write_json(game_info_path, game_info)
def launch_crash_reporter(error_code, error_message):
    # Registers crash reporter to run on exit
```
- All actions and errors are logged to `downloadmanager.log` and the console
- Crash reporter is triggered on fatal errors for diagnostics

#### Settings & Configuration
Reads user preferences for download speed and threads:
```json
{
  "downloadLimit": 5000,   // in KB/s
  "threadCount": 8
}
```
- Reads from `ascendarasettings.json` in Electron APPDATA
- Defaults to unlimited speed and 8 threads if not set

#### Logging
Detailed logging for transparency and troubleshooting:
```python
logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s %(levelname)s %(message)s",
    handlers=[logging.FileHandler(LOG_PATH), logging.StreamHandler(sys.stdout)]
)
```
- Logs both to file and console with timestamps and error details

### Download Process
1. **Initialize** downloader, read user settings, and prepare state file
2. **Start Download** with SmartDL, updating progress in real time
3. **Detect and Extract** archives after download completes
4. **Clean Up** unnecessary files/folders and flatten directories
5. **Verify** all extracted files by size and update state
6. **Update State** and send notifications as needed

### Advanced Features
- **Automatic resume** and smart retry logic for reliability
- **Safe, atomic JSON writes** for all state and progress
- **Crash reporter** integration for diagnostics and user support
- **Adaptive performance** based on user settings

### Security & Performance
- Atomic file operations and permission handling
- Memory-efficient extraction and verification
- Batched file verification and smart progress calculations


- **Automatic Extraction**: Upon download completion, the tool detects and extracts supported archives (`.zip`, `.rar`), cleans up unnecessary files (like `_CommonRedist` and `.url`), and flattens nested folders for easy access.
- **Verification**: After extraction, the downloader checks all files against the expected sizes (from a generated `filemap.ascendara.json`), reporting any discrepancies directly in the state file.

### Extending or Debugging
- **Extend**: To add new features (e.g., support for more archive formats or notification types), start with the `SmartDLDownloader` class and its helper functions.
- **Debug**: Review the log file and state JSONs for step-by-step diagnostics. Error handling is centralized, making it easy to trace issues.