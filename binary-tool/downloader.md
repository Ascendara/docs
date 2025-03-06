# Ascendara Downloader

## Overview
The Ascendara Downloader is a high-performance multi-threaded downloader responsible for downloading, verifying, and extracting game files. It features a robust download system with progress tracking, verification, and automatic error handling.

## Features
- Multi-threaded downloads with configurable thread count
- Progress tracking with speed and ETA
- File verification and integrity checks
- Content-Type validation
- Automatic error reporting with crash reporter integration
- Resume capability
- Archive extraction (.rar, .zip)
- Desktop notifications with theme support

## Usage

### Command Line Arguments
```bash
AscendaraDownloader.exe [link] [game] [online] [dlc] [isVr] [version] [size] [download_dir] [--withNotification theme]
```

### Parameters
- `link`
 Download URL
- `game`
 Game identifier
- `online`
 Online-only flag (true/false)
- `dlc`
 DLC identifier (true/false)
- `isVr`
 VR game flag (true/false)
- `version`
 Game version
- `size`
 Expected file size
- `download_dir`
 Target download directory
- `--withNotification`
 Optional theme for notifications (light, dark, blue)

### Example
```bash
AscendaraDownloader.exe "https://example.com/game.rar" "MyGame" false false false "1.0" "1000000" "C:/Games" --withNotification dark
```

## Development

### Key Classes

#### DownloadManager
Manages multi-threaded downloads with configurable thread count:
```python
class DownloadManager:
    def __init__(self, url, total_size, num_threads=None):
        self.url = url
        self.total_size = total_size
        # Thread count from settings or default to 4
        self.num_threads = self._get_thread_count()
        self.chunks = []
        self.downloaded_size = 0
        self.lock = threading.Lock()
        self.max_retries = 3  # Maximum number of retries per chunk

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
Represents a chunk of the download:
```python
class DownloadChunk:
    def __init__(self, start, end, url):
        self.start = start
        self.end = end
        self.url = url
        self.data = b""
        self.downloaded = 0
```

### Progress Tracking
The downloader maintains a JSON file with detailed progress information:
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
        "downloading": False,
        "extracting": False,
        "updating": False,
        "verifying": False,
        "progressCompleted": "0.00",
        "progressDownloadSpeeds": "0.00 KB/s",
        "timeUntilComplete": "0s"
    }
}
```

### Download Process
1. Validates content type and gets file size
2. Determines archive type (.rar/.zip)
3. Initializes DownloadManager with configured thread count
4. Splits download into equal chunks
5. Downloads chunks in parallel using ThreadPoolExecutor
6. Updates progress with speed and ETA
7. Combines chunks into final file
8. Extracts archive contents
9. Verifies extracted files
10. Sends completion notification

### Error Handling
Implements comprehensive error handling with crash reporter integration:
```python
def handleerror(game_info, game_info_path, e):
    game_info['downloadingData'] = {
        "error": True,
        "message": str(e)
    }
    safe_write_json(game_info_path, game_info)
    
def launch_crash_reporter(error_code, error_message):
    # Register crash reporter to launch on exit
    atexit.register(_launch_crash_reporter_on_exit, error_code, error_message)
```

### Notifications
Supports desktop notifications with theme customization:
```python
def _launch_notification(theme, title, message):
    subprocess.Popen(
        [notification_helper_path, "--theme", theme, "--title", title, "--message", message],
        creationflags=subprocess.CREATE_NO_WINDOW
    )
```

### Configuration
Thread count is configurable via settings:
```python
settings_path = os.path.join(os.getenv('APPDATA'), 'ascendara', 'ascendarasettings.json')
settings = {
    "threadCount": 4  # Default value
}
```