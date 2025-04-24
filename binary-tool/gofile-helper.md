# Ascendara GoFile Helper

## Overview
The GoFile Helper is a specialized tool for handling downloads from GoFile.io. It provides robust download management with advanced features like speed limiting, smart retry mechanisms, and comprehensive logging. The tool seamlessly integrates with Ascendara's notification and crash reporting systems.

## Features
- Smart download management with configurable speed limits
- Adaptive multi-threaded downloads with progress tracking
- Recursive folder structure handling
- Real-time progress tracking with smooth speed calculations and ETA
- Smart resume capability with exponential backoff retries
- Cross-platform archive extraction with automatic tool management
- Automatic CommonRedist cleanup
- Enhanced error handling with detailed logging
- Themed desktop notifications with error reporting
- Memory-efficient file operations with batched verification

## Usage

### Command Line Arguments
```bash
AscendaraGofileHelper.exe [url] [game] [online] [dlc] [isVr] [updateFlow] [version] [size] [download_dir] [--password PASSWORD] [--withNotification THEME]
```

### Parameters
- `url`
 GoFile.io URL (supports both http and https)
- `game`
 Game identifier/name
- `online`
 Online-only flag (true/false)
- `dlc`
 DLC identifier (true/false)
- `isVr`
 VR game flag (true/false)
- `updateFlow`
 Update mode flag (true/false)
- `version`
 Game version string
- `size`
 Expected file size
- `download_dir`
 Target directory for downloads
- `--password`
 Optional password for protected files (will be hashed with SHA256)
- `--withNotification`
 Optional theme for notifications (light, dark, blue)

### Example
```bash
AscendaraGofileHelper.exe "https://gofile.io/d/abc123" "MyGame" false false false false "1.0" "1000000" "C:/Games" --password "mypass" --withNotification dark
```

## Development

### GofileDownloader Class
Main class for handling GoFile downloads with smart settings and progress tracking:
```python
class GofileDownloader:
    def __init__(self, game, online, dlc, isVr, updateFlow, version, size, download_dir, max_workers=5):
        self._max_retries = 3
        self._download_timeout = 30
        self._token = self._getToken()
        self._lock = Lock()
        self._rate_window = []  # For smooth speed calculations
        self._rate_window_size = 5
        self._current_file_progress = {}
        self._total_downloaded = 0
        self._total_size = 0
        self._download_speed_limit = self._get_speed_limit()
        self.updateFlow = updateFlow
        # Other initialization...
```

### Key Components

#### Progress Tracking
Implements real-time progress tracking with smooth speed calculations:
```python
def _update_progress(self, filename, progress, rate, eta_seconds=0, done=False):
    # Thread-safe progress updates
    # Smooth speed calculations using sliding window
    # Smart ETA formatting with multiple time units
    # Automatic JSON state persistence
```

#### Download Management
Handles downloads with smart retry logic and speed control:
```python
def _downloadContent(self, file_info, chunk_size=None):
    # Configurable speed limiting
    # Exponential backoff for retries
    # Resume support with range headers
    # Memory-efficient chunk processing
    # Real-time progress updates
```

#### Archive Extraction
Cross-platform archive handling with automatic tool management:
```python
def _extract_files(self):
    # Automatic extraction tool detection/installation
    # Platform-specific optimizations (Windows/Linux/macOS)
    # CommonRedist cleanup
    # Efficient file verification
```

### Error Handling
Comprehensive error handling with detailed logging:
```python
def handleerror(game_info, game_info_path, e):
    # Reset game state
    game_info['online'] = ""
    game_info['dlc'] = ""
    game_info['isRunning'] = False
    game_info['version'] = ""
    game_info['executable'] = ""
    game_info['downloadingData'] = {
        "error": True,
        "message": str(e)
    }
    # Persist error state
    safe_write_json(game_info_path, game_info)
```

### File Operations
Thread-safe file operations with integrity checks:
```python
def safe_write_json(filepath, data):
    # Atomic file writes using temporary files
    # Multiple retry attempts with backoff
    # Directory creation if needed
    # Error handling for permissions
```

### Configuration
Settings management with smart defaults:
```python
# Load settings from ascendarasettings.json
settings_path = os.path.join(os.getenv('APPDATA'), 'ascendara', 'ascendarasettings.json')
settings = {
    "downloadLimit": 0,  # KB/s, 0 means unlimited
    # Other settings...
}
```

### Logging
Comprehensive logging system with both file and console output:
```python
def setup_logging():
    # Timestamp-based log files
    # Both console and file logging
    # Different log levels (DEBUG/INFO)
    # Detailed error tracking
```

### Download Process
1. Initialize downloader with smart settings and state management
2. Parse and validate GoFile URL
3. Authenticate and obtain session token
4. Handle password protection with SHA256 hashing
5. Parse folder structure recursively
6. Calculate total download size
7. Download files with speed limiting and progress tracking
8. Extract archives with platform-specific optimizations
9. Clean up CommonRedist directories
10. Verify extracted files in batches
11. Update game state and send notifications

### Cross-Platform Support
- **Windows**: Native support for .rar and .zip extraction
- **macOS**: Automatic installation of 'unar' via Homebrew
- **Linux**: Automatic installation of 'unrar' via package managers

### Security Features
- Password hashing with SHA256
- Secure token management
- Atomic file operations
- Permissions handling

### Performance Optimizations
- Configurable download speed limits
- Memory-efficient chunk processing
- Batched file verification
- Smart progress calculations
- Exponential backoff for retries



### Progress Tracking
The tool maintains a JSON file with detailed download progress:
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
        "downloading": False,
        "verifying": False,
        "extracting": False,
        "updating": False,
        "progressCompleted": "0.00",
        "progressDownloadSpeeds": "0.00 KB/s",
        "timeUntilComplete": "0s"
    }
}
```

### Advanced Features

#### Adaptive Download Rate Calculation
Uses a sliding window approach for smoother speed estimates:
```python
def _update_progress(self, filename, progress, rate, eta_seconds=0, done=False):
    # Format speed with consistent decimal places and thresholds
    def format_speed(rate):
        if rate < 0.1:  # Very slow speeds
            return "0.00 B/s"
        elif rate < 1024:
            return f"{rate:.2f} B/s"
        elif rate < 1024 * 1024:
            return f"{(rate / 1024):.2f} KB/s"
        else:
            return f"{(rate / (1024 * 1024)):.2f} MB/s"
            
    # Format ETA with improved granularity
    if done:
        eta = "0s"
    elif eta_seconds <= 0:
        eta = "calculating..."
    elif eta_seconds < 60:
        eta = f"{int(eta_seconds)}s"
    # Additional time formatting...
```

#### Extraction and Verification
Handles both ZIP and RAR archives with platform-specific extraction:
```python
def _extract_files(self):
    # Create watching file for tracking extracted files
    watching_path = os.path.join(self.download_dir, "filemap.ascendara.json")
    watching_data = {}
    
    # Extract all archives with platform-specific handling
    if sys.platform == "win32":
        # Windows-specific extraction for ZIP and RAR
    else:
        # Cross-platform extraction with patoolib
        
    # Verify extracted files
    self._verify_extracted_files(watching_path)
```

### Error Handling and Logging
Comprehensive error handling with crash reporter integration and detailed logging:
```python
def setup_logging():
    # Configure logging to both console and temp file
    timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
    temp_log_path = os.path.join(gettempdir(), f'ascendara_gofile_{timestamp}.log')
    
    # Configure handlers and formatters
    
def launch_crash_reporter(error_code, error_message):
    # Register crash reporter to launch on exit
    atexit.register(_launch_crash_reporter_on_exit, error_code, error_message)
```