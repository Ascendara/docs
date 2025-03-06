# Ascendara GoFile Helper

## Overview
The GoFile Helper is a specialized tool for handling downloads from GoFile.io. It supports multi-threaded downloads, password-protected files, folder structures, and integrates with Ascendara's notification and crash reporting systems.

## Features
- Multi-threaded downloads with progress tracking
- Password-protected file support
- Recursive folder downloading
- Detailed progress tracking with speed and ETA
- Resume capability for interrupted downloads
- Automatic error handling with crash reporter integration
- Desktop notifications with theme support
- Detailed logging for troubleshooting

## Usage

### Command Line Arguments
```bash
AscendaraGofileHelper.exe [url] [game] [online] [dlc] [isVr] [version] [size] [download_dir] [--password PASSWORD] [--withNotification THEME]
```

### Parameters
- `url`
 GoFile.io URL
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
 Target directory
- `--password`
 Optional password for protected files
- `--withNotification`
 Optional theme for notifications (light, dark, blue)

### Example
```bash
AscendaraGofileHelper.exe "https://gofile.io/d/abc123" "MyGame" false false false "1.0" "1000000" "C:/Games" --password "mypass" --withNotification dark
```

## Development

### GofileDownloader Class
Main class for handling GoFile downloads:
```python
class GofileDownloader:
    def __init__(self):
        self.qbt_client = None
        self.connect_thread = None
        self.current_torrent_hash = None
```

### Key Methods

#### Download from GoFile
```python
def download_from_gofile(self, url, password=None, withNotification=None):
    # Handle URL formatting
    if url.startswith("//"):
        url = "https:" + url
    
    content_id = url.split("/")[-1]
    _password = sha256(password.encode()).hexdigest() if password else None
    
    # Get file information and download
    files_info = self._parseLinksRecursively(content_id, _password)
    
    # Calculate total size for progress tracking
    self._total_size = 0
    self._total_downloaded = 0
    for item in files_info.values():
        # Check if file exists and get size from headers
        
    # Download each file
    total_files = len(files_info)
    current_file = 0
    for item in files_info.values():
        current_file += 1
        self._downloadContent(item)
        
    # Extract downloaded archives
    self._extract_files()
```

#### Parse Links Recursively
```python
def _parseLinksRecursively(self, content_id, password, current_path=""):
    url = f"https://api.gofile.io/contents/{content_id}?wt=4fd6sg89d7s6&cache=true"
    if password:
        url = f"{url}&password={password}"
        
    headers = {
        "User-Agent": os.getenv("GF_USERAGENT", "Mozilla/5.0"),
        "Authorization": f"Bearer {self._token}",
    }
    
    response = requests.get(url, headers=headers).json()
    
    # Handle folders and files recursively
    if data["type"] == "folder":
        # Process folder contents recursively
    else:
        # Process single file
```

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