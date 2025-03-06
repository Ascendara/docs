# Ascendara Game Handler

## Overview
The Game Handler manages game execution, process monitoring, and Discord Rich Presence integration. It supports both standard Ascendara games and custom games, with optional shortcut functionality and game save backup via Ludusavi.

## Features
- Game process management
- Discord Rich Presence integration
- Custom game support
- Shortcut launch support
- Running status tracking
- Play time and launch count tracking
- Ludusavi game save backup integration
- Error handling and reporting

## Usage

### Command Line Arguments
```bash
AscendaraGameHandler.exe [game_path] [is_custom_game] [--shortcut] [--ludusavi]
```

### Parameters
- `game_path`
 Path to game executable
- `is_custom_game`
 Custom game flag (true/false or 1/0)
- `--shortcut`
 Optional shortcut flag for Discord integration
- `--ludusavi`
 Optional flag to enable Ludusavi backup after game closes

### Example
```bash
AscendaraGameHandler.exe "C:/Games/MyGame/game.exe" false --shortcut --ludusavi
```

## Development

### Core Functions

#### Execute Game
Main function for game execution and monitoring:
```python
def execute(game_path, is_custom_game, is_shortcut=False, use_ludusavi=False):
    # Setup Discord RPC if shortcut mode
    rpc = setup_discord_rpc() if is_shortcut else None
    
    # Handle standard vs custom games
    if not is_custom_game:
        # Find game info from .ascendara.json by searching up directories
        game_dir, exe_name = os.path.split(game_path)
        exe_path = os.path.join(game_dir, exe_name)
        
        # Smart search for the game's JSON file
        current_dir = game_dir
        found_json = False
        
        while current_dir and os.path.dirname(current_dir) != current_dir:
            dir_name = os.path.basename(current_dir)
            potential_json = os.path.join(current_dir, f"{dir_name}.ascendara.json")
            
            if os.path.exists(potential_json):
                json_file_path = potential_json
                game_name = dir_name
                found_json = True
                break
            
            # Check parent directory
            # ...
    else:
        # Load custom game info from settings
        exe_path = game_path
        user_data_dir = os.path.join(os.environ['APPDATA'], 'ascendara')
        settings_file = os.path.join(user_data_dir, 'ascendarasettings.json')
        # Load game entry from games.json
        # ...
    
    # Update running status and launch count
    # ...
    
    # Launch game process
    process = subprocess.Popen(exe_path)
    
    # Monitor process and track play time
    start_time = time.time()
    last_update = start_time
    last_play_time = 0
    
    while process.poll() is None:
        current_time = time.time()
        elapsed = int(current_time - last_update)
        if elapsed >= 1:
            last_play_time = elapsed
            # Update play time in JSON
            # ...
            last_update = current_time
        time.sleep(0.1)
    
    # Cleanup on exit
    # ...
    
    # Run Ludusavi backup if enabled
    if use_ludusavi and game_name:
        run_ludusavi_backup(game_name)
```

### Discord Integration

#### Setup RPC
```python
def setup_discord_rpc():
    try:
        rpc = Presence(CLIENT_ID)
        rpc.connect()
        return rpc
    except Exception as e:
        logging.error(f"Failed to connect to Discord RPC: {e}")
        return None
```

#### Update Presence
```python
def update_discord_presence(rpc, game_name):
    if rpc:
        try:
            rpc.update(
                details="Playing a Game",
                state=game_name,
                start=int(time.time()),
                large_image="ascendara",
                large_text="Ascendara",
                buttons=[{"label": "Play on Ascendara", "url": "https://ascendara.app/"}]
            )
        except Exception as e:
            logging.error(f"Failed to update Discord presence: {e}")
```

### Process Management

#### Process Monitoring
Using psutil for reliable process monitoring:
```python
def is_process_running(exe_path):
    for proc in psutil.process_iter(['name']):
        try:
            if proc.info['name'] == os.path.basename(exe_path):
                return True
        except (psutil.NoSuchProcess, psutil.AccessDenied, psutil.ZombieProcess):
            pass
    return False
```

#### Play Time Tracking
```python
def update_play_time(file_path, is_custom_game, game_entry=None):
    """Update the playTime field in either the game's JSON file or games.json for custom games"""
    try:
        with open(file_path, "r") as f:
            data = json.load(f)
        
        if is_custom_game:
            # For custom games, update the specific game entry in games.json
            for game in data["games"]:
                if game["executable"] == game_entry["executable"]:
                    if "playTime" not in game:
                        game["playTime"] = 0
                    game["playTime"] += 1
                    break
        else:
            # For regular games, update the game-specific json
            if "playTime" not in data:
                data["playTime"] = 0
            data["playTime"] += 1
        
        with open(file_path, "w") as f:
            json.dump(data, f, indent=4)
    except Exception as e:
        logging.error(f"Failed to update play time: {e}")
```

### Ludusavi Integration

#### Backup Configuration
```python
def get_ludusavi_settings():
    try:
        settings_path = os.path.join(os.getenv('APPDATA'), 'ascendara', 'ascendarasettings.json')
        if os.path.exists(settings_path):
            with open(settings_path, 'r') as f:
                settings = json.load(f)
                ludusavi_settings = settings.get('ludusavi')
                # Explicitly check if enabled is True, return None otherwise
                if ludusavi_settings and ludusavi_settings.get('enabled') is True:
                    return ludusavi_settings
        return None
    except Exception as e:
        logging.error(f"Failed to load Ludusavi settings: {e}")
        return None
```

#### Run Backup
```python
def run_ludusavi_backup(game_name):
    """Run Ludusavi backup for a specific game"""
    ludusavi_settings = get_ludusavi_settings()
    if not ludusavi_settings:
        logging.info("Ludusavi backup skipped: not enabled in settings")
        return False
    
    try:
        # Check if Ludusavi is installed
        ludusavi_path = os.path.join("./ludusavi.exe")
        
        # Prepare backup command with settings from configuration
        backup_location = ludusavi_settings.get('backupLocation')
        backup_format = ludusavi_settings.get('backupFormat', 'zip')
        backups_to_keep = ludusavi_settings.get('backupOptions', {}).get('backupsToKeep', 5)
        
        # Map compression settings
        compression_level = ludusavi_settings.get('backupOptions', {}).get('compressionLevel', 'default')
        if compression_level == 'default':
            compression_level = 'deflate'
        
        # Run the backup process
        cmd = [
            ludusavi_path,
            "backup",
            "--path", backup_location,
            "--format", backup_format,
            "--full-limit", str(backups_to_keep),
            "--compression", compression_level,
            "--force",
            game_name
        ]
        
        result = subprocess.run(cmd, capture_output=True, text=True)
        return result.returncode == 0
    except Exception as e:
        logging.error(f"Error running Ludusavi backup: {e}")
        return False
```

### Status Tracking
The handler maintains game status in multiple locations:

1. Game-specific JSON:
```python
game_data = {
    "game": game_name,
    "isRunning": True,
    "launchCount": 5,  # Incremented each launch
    "playTime": 120    # Updated during gameplay
    # Other game-specific data
}
```

2. Global settings JSON:
```python
settings_data = {
    "runningGames": {
        "game_name": "C:/path/to/game.exe"  # Full path stored
    }
    # Other settings
}
```

### Error Handling
Implements crash reporting with detailed logging:
```python
def launch_crash_reporter(error_code, error_message):
    """Register the crash reporter to launch on exit with the given error details"""
    if not hasattr(launch_crash_reporter, "_registered"):
        atexit.register(_launch_crash_reporter_on_exit, error_code, error_message)
        launch_crash_reporter._registered = True

def _launch_crash_reporter_on_exit(error_code, error_message):
    try:
        crash_reporter_path = os.path.join('./AscendaraCrashReporter.exe')
        if os.path.exists(crash_reporter_path):
            subprocess.Popen(
                [crash_reporter_path, "gamehandler", str(error_code), error_message],
                creationflags=subprocess.CREATE_NO_WINDOW
            )
    except Exception as e:
        logging.error(f"Failed to launch crash reporter: {e}")
```

### Configuration
Settings are stored in the user's AppData directory:
```python
user_data_dir = os.path.join(os.environ['APPDATA'], 'ascendara')
settings_file = os.path.join(user_data_dir, 'ascendarasettings.json')
```