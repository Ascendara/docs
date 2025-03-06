# Ascendara Torrent Handler

## Overview
The Torrent Handler is a command-line tool for managing game downloads through torrent files in Ascendara. It integrates with qBittorrent for reliable downloads, provides real-time progress tracking, and handles automated game installation with silent setup execution.

## Features
- Automated torrent download management
- Real-time download progress tracking
- Silent game installation with automatic setup detection
- qBittorrent integration with sequential downloading
- Comprehensive error handling and crash reporting
- JSON-based game state management
- Desktop notifications with theme support
- Automatic cleanup for incomplete downloads
- Detailed logging system

## Prerequisites
- qBittorrent installed with Web UI enabled (default port: 8080)
- Default qBittorrent credentials (admin/adminadmin)
- Python with required packages:
  - qbittorrentapi
  - argparse

## Usage

### Command Line Arguments
```bash
AscendaraTorrentHandler.exe <magnet> <game> <online> <dlc> <version> <size> <dir> [--withNotification theme]
```

### Parameters
- `magnet`: Magnet link for the torrent
- `game`: Game name
- `online`: Boolean flag for online games
- `dlc`: Boolean flag for DLC content
- `version`: Game version
- `size`: Download size
- `dir`: Download directory
- `--withNotification`: (Optional) Theme name for notifications (e.g., light, dark, blue)

### Example
```bash
AscendaraTorrentHandler.exe "magnet:?xt=urn:btih:..." "MyGame" true false "1.0.0" "50GB" "C:/Games" --withNotification emerald
```

## Implementation

### Core Components

#### TorrentManager Class
Manages torrent downloads, installation, and cleanup:
```python
class TorrentManager:
    def __init__(self):
        self.qbt_client = None
        self.connect_thread = None
        self.current_torrent_hash = None
        self.notification_theme = None
        
    def cleanup(self):
        if self.qbt_client and self.current_torrent_hash:
            try:
                # Get torrent info to check if it's complete
                torrents = self.qbt_client.torrents_info(torrent_hashes=self.current_torrent_hash)
                if torrents and not torrents[0].state_enum.is_complete:
                    # Delete the torrent and its data if download is incomplete
                    self.qbt_client.torrents_delete(delete_files=True, torrent_hashes=self.current_torrent_hash)
            except:
                pass  # Ignore any errors during cleanup
```

#### Game State Management
Game state is tracked in a JSON file (`<game>.ascendara.json`):
```json
{
    "game": "GameName",
    "online": true,
    "dlc": false,
    "version": "1.0.0",
    "size": "50GB",
    "executable": "path/to/game.exe",
    "isRunning": false,
    "downloadingData": {
        "downloading": true,
        "waiting": true,
        "extracting": false,
        "updating": false,
        "progressCompleted": "0.00",
        "progressDownloadSpeeds": "0.00 KB/s",
        "timeUntilComplete": "0s"
    }
}
```

#### Notification Integration
Integrates with Ascendara's notification system for status updates:
```python
def _launch_notification(theme, title, message):
    try:
        # Get the directory where the current executable is located
        exe_dir = os.path.dirname(os.path.abspath(sys.argv[0]))
        notification_helper_path = os.path.join(exe_dir, 'AscendaraNotificationHelper.exe')
        
        if os.path.exists(notification_helper_path):
            subprocess.Popen(
                [notification_helper_path, "--theme", theme, "--title", title, "--message", message],
                creationflags=subprocess.CREATE_NO_WINDOW
            )
    except Exception as e:
        logging.error(f"Failed to launch notification helper: {e}")
```

### Error Handling
Integrates with Ascendara's crash reporting system:
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
```

### Installation Process
The tool automatically detects and runs setup executables:
```python
# Find setup executable
setup_file = None
torrent_folder = os.path.join(game_dir, torrent.name)
for file in os.listdir(torrent_folder):
    if file.lower().startswith(('setup', game.lower())) and file.lower().endswith('.exe'):
        setup_file = os.path.join(torrent_folder, file)
        break

# Run setup silently with target directory
install_dir = os.path.join(game_dir, game)
os.makedirs(install_dir, exist_ok=True)

process = subprocess.Popen([setup_file, '/VERYSILENT', f'/DIR="{install_dir}"'], 
                        stdout=subprocess.PIPE, 
                        stderr=subprocess.PIPE)
```

## Development

### Adding New Features
1. Extend the TorrentManager class
2. Update game state management
3. Implement error handling
4. Test with various game scenarios

### Best Practices
- Use safe_write_json for file operations
- Implement proper cleanup on exit
- Handle qBittorrent connection failures
- Validate all command line arguments
- Provide user feedback through notifications

## Troubleshooting

### Common Issues
1. qBittorrent Connection Failed
   - Ensure qBittorrent is running
   - Check Web UI credentials
   - Verify port settings (default: 8080)

2. Installation Errors
   - Check write permissions
   - Verify setup executable exists
   - Ensure sufficient disk space
   - Check setup executable naming (should start with 'setup' or game name)

### Error Codes
Error codes are passed to the crash reporter:
- Connection errors (1500-1503)
- Download failures (1501-1502)
- Installation failures (1506)
- State management errors (1504-1505)

### Logging
The tool creates detailed logs in the temp directory:
```python
def setup_logging():
    # Create temp log file with timestamp
    timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
    temp_log_path = os.path.join(gettempdir(), f'ascendara_torrent_{timestamp}.log')
    
    # Configure root logger
    root_logger = logging.getLogger()
    root_logger.setLevel(logging.DEBUG)
```