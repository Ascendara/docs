# Ascendara Downloader

## Overview
The Ascendara Downloader is a high-performance multi-threaded downloader responsible for downloading, verifying, and extracting game files. It features a robust download system with progress tracking, verification, and automatic error handling.

## Features
- Smart multi-threaded downloads with configurable thread count and chunk size management
- Adaptive download speed limiting with user-configurable settings
- Real-time progress tracking with speed, ETA, and smooth updates
- Comprehensive file verification and integrity checks
- Content-Type validation and SSL security
- Automatic error reporting with crash reporter integration
- Smart resume capability with retry mechanisms
- Archive extraction (.rar, .zip) with CommonRedist cleanup
- Themed desktop notifications with customizable appearance
- Memory-efficient file processing with batched operations

## Usage

### Command Line Arguments
```bash
AscendaraDownloader.exe [link] [game] [online] [dlc] [isVr] [updateFlow] [version] [size] [download_dir] [--withNotification theme]
```

### Parameters
- `link`
 Download URL for the game file
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
 Target download directory
- `--withNotification`
 Optional theme for notifications (light, dark, blue)

### Example
```bash
AscendaraDownloader.exe "https://example.com/game.rar" "MyGame" false false false false "1.0" "1000000" "C:/Games" --withNotification dark
```

## Development

### Key Classes

#### DownloadManager
Manages smart multi-threaded downloads with configurable settings and adaptive behavior:
```python
class DownloadManager:
    def __init__(self, url, total_size, num_threads=None, max_chunk_size=64*1024*1024):
        self.url = url
        self.total_size = total_size
        self.max_chunk_size = max_chunk_size
        # Smart settings from ascendarasettings.json
        # - Thread count (default: 8)
        # - Chunk size (default: 8MB)
        # - Download speed limit (KB/s, 0 = unlimited)
        self.num_threads = self._get_thread_count()
        self.chunks = []
        self.downloaded_size = 0
        self.lock = threading.Lock()
        self.max_retries = 4  # Maximum number of retries per chunk
        self.per_thread_speeds = {}
        self.range_supported = True
        self.current_speed = 0.0
        self.download_speed_limit = self._get_speed_limit()

    def split_chunks(self):
        # Split download into equal chunks
        chunk_size = self.total_size // self.num_threads
        for i in range(self.num_threads):
            start = i * chunk_size
            end = start + chunk_size - 1 if i < self.num_threads - 1 else self.total_size - 1
            self.chunks.append(DownloadChunk(start, end, self.url))

    def download_chunk(self, chunk, session, callback=None):
        # Download a specific chunk with range headers
        headers = {'Range': f'bytes={chunk.start}-{chunk.end}'}
        response = session.get(chunk.url, headers=headers, stream=True)
        
        for data in response.iter_content(chunk_size=1024*1024):
            if not data:
                break
            chunk.data += data
            chunk.downloaded += len(data)
            with self.lock:
                self.downloaded_size += len(data)
                if callback:
                    callback(len(data))
```

#### DownloadChunk
Represents a chunk of the download with smart file handling:
```python
class DownloadChunk:
    def __init__(self, start, end, url, chunk_id, temp_dir):
        self.start = start
        self.end = end
        self.url = url
        self.chunk_id = chunk_id
        self.temp_file_path = os.path.join(temp_dir, f"chunk_{chunk_id}.tmp")
        self.downloaded = 0
```

### Progress Tracking
The downloader maintains a detailed JSON file with real-time progress information and thread-safe updates:
```python
game_info = {
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
        "extracting": False,
        "updating": updateFlow,
        "verifying": False,
        "progressCompleted": "0.00",
        "progressDownloadSpeeds": "0.00 KB/s",
        "timeUntilComplete": "calculating..."
    }
}
```

### Download Process
1. Validates content type, SSL security, and gets file size
2. Pre-allocates file space for efficient writing
3. Initializes DownloadManager with smart settings
4. Splits download into optimized chunks based on size
5. Downloads chunks in parallel with adaptive speed control
6. Provides real-time progress updates with smooth speed calculations
7. Implements smart retry logic for failed chunks
8. Efficiently merges chunks with minimal memory usage
9. Extracts archives with CommonRedist cleanup
10. Performs batched file verification
11. Sends themed completion notifications

### Error Handling
Implements comprehensive error handling with smart retries and crash reporting:
```python
def handleerror(game_info, game_info_path, e):
    game_info['online'] = ""
    game_info['dlc'] = ""
    game_info['isRunning'] = False
    game_info['version'] = ""
    game_info['executable'] = ""
    game_info['downloadingData'] = {
        "error": True,
        "message": str(e)
    }
    safe_write_json(game_info_path, game_info)

def launch_crash_reporter(error_code, error_message):
    # Register crash reporter to launch on exit if not already registered
    if not hasattr(launch_crash_reporter, "_registered"):
        atexit.register(_launch_crash_reporter_on_exit, error_code, error_message)
        launch_crash_reporter._registered = True
```

### Notifications
Supports desktop notifications with theme customization and error handling:
```python
def _launch_notification(theme, title, message):
    try:
        exe_dir = os.path.dirname(os.path.abspath(sys.argv[0]))
        notification_helper_path = os.path.join(exe_dir, 'AscendaraNotificationHelper.exe')
        logging.debug(f"Looking for notification helper at: {notification_helper_path}")
        
        if os.path.exists(notification_helper_path):
            subprocess.Popen(
                [notification_helper_path, "--theme", theme, "--title", title, "--message", message],
                creationflags=subprocess.CREATE_NO_WINDOW
            )
        else:
            logging.error(f"Notification helper not found at: {notification_helper_path}")
    except Exception as e:
        logging.error(f"Failed to launch notification helper: {e}")
```

### Configuration
Settings are managed through a JSON configuration file:
```python
settings_path = os.path.join(os.getenv('APPDATA'), 'Electron', 'ascendarasettings.json')
settings = {
    "threadCount": 8,  # Default thread count
    "downloadLimit": 0  # Default download speed limit (KB/s, 0 = unlimited)
}
```

### File Handling
Implements efficient file operations with thread-safe JSON writes:
```python
def safe_write_json(filepath, data, max_retries=5):
    # Thread-safe JSON file writing with retries and error handling
    # Uses file locking and atomic operations
    # Supports exponential backoff for retries
    # Ensures data integrity with temporary files
    pass

def _verify_extracted_files(watching_path, download_path, game_info, game_info_path, game, archive_file_path):
    # Batch verification of extracted files
    # Automatic cleanup of CommonRedist directories
    # Detailed error reporting for failed verifications
    pass
```