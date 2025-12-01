# Ascendara Local Refresh

## Overview
The Ascendara Local Refresh is a command-line tool for refreshing the local game index by scraping SteamRIP. It uses multi-threaded processing to efficiently fetch, parse, and store game data along with cover images for offline use within the Ascendara ecosystem.

## Features
- **Multi-threaded scraping** with configurable worker count
- **Bidirectional processing** - processes posts from both ends simultaneously for faster completion
- **Cloudflare bypass** using cloudscraper with cookie authentication
- **Real-time progress tracking** via JSON progress file
- **Automatic image downloading** with rate limiting
- **Comprehensive game data extraction** (download links, versions, system requirements)
- **Error handling** with crash reporter integration
- **Category mapping** from WordPress API
- **View count fetching** for game popularity weighting
- **Game ID encoding** - Converts numeric post IDs to 6-character identifiers
- **Blacklist support** - Skips games blacklisted in user settings

## Usage

### Command Line Arguments
```bash
AscendaraLocalRefresh.exe --output [dir] --cookie [cookie] [--workers N] [--per-page N]
```

### Parameters
- `--output, -o` (required)
  Output directory for JSON data and images
- `--cookie, -c` (required)
  cf_clearance cookie value for Cloudflare bypass
- `--workers, -w` (optional)
  Number of worker threads (default: 8)
- `--per-page, -p` (optional)
  Number of posts to fetch per page (default: 50)

### Example
```bash
AscendaraLocalRefresh.exe --output "C:/Ascendara/LocalIndex" --cookie "cf_clearance=abc123..." --workers 8 --per-page 100
```

## Workflow

### 1. Initialization
- Creates output directory and `imgs` subdirectory
- Initializes progress tracking via `progress.json`
- Creates cloudscraper instance with Cloudflare cookie

### 2. Category Fetching
- Fetches all categories from WordPress API
- Builds category ID to name mapping for game classification

### 3. Post Fetching
- Paginated fetching of all game posts from SteamRIP API
- Handles rate limiting and retries on timeouts
- Real-time progress updates during fetch

### 4. Post Processing
- Bidirectional multi-threaded processing (processes from both ends simultaneously)
- Skips blacklisted games from user settings
- Extracts game metadata, download links, and system requirements
- Downloads cover images with rate limiting
- Fetches view counts for popularity weighting
- Encodes post IDs to 6-character game identifiers

### 5. Output Generation
- Generates `ascendara_games.json` with all game data
- Updates `timestamp.ascendara.json` to mark successful indexing

## Progress Tracking

The tool writes real-time progress to `progress.json`:
```json
{
  "status": "running",
  "phase": "processing_posts",
  "totalPosts": 1500,
  "processedPosts": 750,
  "totalImages": 750,
  "downloadedImages": 720,
  "currentGame": "Game Name",
  "progress": 0.5,
  "elapsedSeconds": 120.5,
  "errors": [],
  "timestamp": 1699999999.999
}
```

### Status Values
- `initializing` - Setting up scraper and directories
- `running` - Actively processing
- `completed` - Successfully finished
- `failed` - Encountered fatal error
- `error` - Cloudflare verification failed

### Phase Values
- `starting` - Initial setup
- `fetching_categories` - Loading category mappings
- `fetching_posts` - Downloading post data from API
- `processing_posts` - Extracting game data from posts
- `saving` - Writing final output file
- `done` - Process complete

## Output Format

### Game Entry Structure
```json
{
  "game": "Game Name",
  "size": "12 GB",
  "version": "1.0.0",
  "online": false,
  "dlc": true,
  "dirlink": "https://steamrip.com/game-name/",
  "download_links": {
    "gofile": ["https://gofile.io/..."],
    "qiwi": ["https://qiwi.gg/..."],
    "megadb": ["https://megadb.net/..."]
  },
  "weight": "15000",
  "imgID": "abc123xyz",
  "gameID": "AbCdEf",
  "category": ["Action", "Adventure"],
  "latest_update": "2024-01-15",
  "minReqs": {
    "os": "Windows 10",
    "cpu": "Intel Core i5",
    "ram": "8 GB RAM",
    "gpu": "NVIDIA GTX 1060",
    "directx": "Version 11",
    "storage": "50 GB available space"
  }
}
```

### Metadata Structure
```json
{
  "getDate": "January 15, 2024, 03:30 PM",
  "local": true,
  "source": "STEAMRIP",
  "games": "3500"
}
```

## Development

### Core Components

#### RefreshProgress Class
Manages thread-safe progress tracking:
```python
class RefreshProgress:
    def __init__(self, output_directory):
        self.progress_file = os.path.join(output_directory, "progress.json")
        self.lock = threading.Lock()
        # Initialize tracking fields
    
    def _update_progress(self):
        """Write progress to file with thread safety"""
        with self.lock:
            # Calculate and write progress data
```

#### Scraper Creation
Creates cloudscraper with Cloudflare bypass:
```python
def create_scraper(cookie):
    scraper = cloudscraper.create_scraper(
        browser={"browser": "chrome", "platform": "windows", "mobile": False}
    )
    scraper.headers.update({
        "User-Agent": "Mozilla/5.0 ...",
        "Cookie": f"cf_clearance={cookie}"
    })
    return scraper
```

#### Post Processing
Extracts game data from WordPress posts:
```python
def process_post(post, scraper, category_map, imgs_dir, progress, blacklist_ids=None):
    # Check if post is blacklisted
    if blacklist_ids and post_id in blacklist_ids:
        return None
    
    # Extract game name, content
    game_name = clean_game_name(title)
    
    # Parse download links, size, version
    download_links = extract_download_links(content)
    game_size = extract_game_size(content)
    version = extract_version(content)
    
    # Check online/DLC status
    is_online = check_online_status(content, title)
    has_dlc = check_dlc_status(content)
    
    # Extract system requirements
    min_reqs = extract_min_requirements(content)
    
    # Download cover image
    img_id = download_image(scraper, image_url, img_id, imgs_dir, progress)
    
    # Encode post_id to 6-character identifier
    encoded_game_id = encode_game_id(post_id)
    
    return game_entry
```

#### Game ID Encoding
Converts numeric post IDs to 6-character identifiers using base-46 encoding with scrambling:
```python
GAME_ID_CHARS = "ABCDEFGHJKMNPQRSTUVWXYZabcdefghjkmnpqrstuvwxyz"  # 46 chars
GAME_ID_LENGTH = 6
SCRAMBLE_MULT = 2971  # Prime number for spreading sequential IDs

def encode_game_id(post_id):
    """Convert numeric post_id to a 6-character identifier."""
    scrambled = (num * SCRAMBLE_MULT) % SCRAMBLE_MOD
    # Convert to base-46
    return ''.join(reversed(result))

def decode_game_id(game_id):
    """Convert 6-character identifier back to numeric post_id."""
    # Reverse the scramble using modular multiplicative inverse
    inv = pow(SCRAMBLE_MULT, -1, SCRAMBLE_MOD)
    return str((num * inv) % SCRAMBLE_MOD)
```

#### Blacklist Support
Reads blacklisted game IDs from user settings:
```python
def get_blacklist_ids():
    """Read blacklisted game IDs from settings file."""
    # Windows: %APPDATA%/Electron/ascendarasettings.json
    # macOS: ~/Library/Application Support/ascendara/ascendarasettings.json
    blacklist = settings.get('blacklistIDs', [])
    # Decode 6-character IDs back to numeric post IDs
    return numeric_ids
```

### Data Extraction Functions

#### Download Links
Extracts links from supported providers:
```python
def extract_download_links(content):
    # Supported providers:
    # - gofile.io
    # - qiwi.gg
    # - megadb.net
    # - pixeldrain.com
    # - buzzheavier.com
    # - vikingfile.com
    # - datanodes.to
    # - 1fichier.com
```

#### System Requirements
Parses minimum requirements from HTML:
```python
def extract_min_requirements(content):
    # Extracts: os, cpu, ram, gpu, directx, storage
    reqs = {}
    os_match = re.search(r'<strong>OS</strong>:?\s*([^<]+)', content)
    # ... additional field extraction
    return reqs
```

### Rate Limiting

#### Image Downloads
Prevents overwhelming the server:
```python
IMAGE_DOWNLOAD_DELAY = 0.15  # seconds between downloads

def download_image(scraper, image_url, img_id, imgs_dir, progress):
    with image_download_lock:
        elapsed = time.time() - last_image_download
        if elapsed < IMAGE_DOWNLOAD_DELAY:
            time.sleep(IMAGE_DOWNLOAD_DELAY - elapsed)
```

### Error Handling

#### Crash Reporter Integration
```python
def launch_crash_reporter(error_code, error_message):
    """Register the crash reporter to launch on exit"""
    if not hasattr(launch_crash_reporter, "_registered"):
        atexit.register(_launch_crash_reporter_on_exit, error_code, error_message)
        launch_crash_reporter._registered = True
```

#### Retry Logic
Handles timeouts and rate limiting:
```python
max_retries = 3
for attempt in range(max_retries):
    try:
        response = scraper.get(url, timeout=30)
        if response.status_code == 429:
            wait_time = (attempt + 1) * 5
            time.sleep(wait_time)
            continue
        # Process response
    except Exception as e:
        if 'timed out' in str(e).lower():
            time.sleep(2 * (attempt + 1))
```

## Configuration

### Cloudflare Cookie
The tool requires a valid `cf_clearance` cookie from SteamRIP to bypass Cloudflare protection. This cookie:
- Must be obtained from a browser session
- Has a limited validity period
- Should be passed without the `cf_clearance=` prefix (added automatically)

### Output Files
- `ascendara_games.json` - Main game index file
- `progress.json` - Real-time progress tracking
- `imgs/` - Directory containing downloaded cover images

### Timestamp File
On successful completion, updates `%USERPROFILE%/timestamp.ascendara.json`:
```json
{
  "hasIndexBefore": true
}
```

## Dependencies
- `cloudscraper` - Cloudflare bypass
- `requests` - HTTP client (via cloudscraper)
- `concurrent.futures` - Thread pool execution
- `argparse` - CLI argument parsing
- `logging` - Debug and error logging
- `shutil` - Directory cleanup (removes existing imgs folder before refresh)
