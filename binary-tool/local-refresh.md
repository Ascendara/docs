# Ascendara Local Refresh

## Overview
Ascendara Local Refresh is a command-line scraper that builds a game index (`ascendara_games.json` plus cover images) from a game source. It fetches and parses the source's game posts with multiple threads, downloads covers, and writes the index in the format Ascendara reads.

> **Not shipped with the app.** Ascendara no longer builds the index on users' computers, and the app no longer has a manual scrape option. The Ascendara API runs the official index refresh on a schedule, and the app downloads the result automatically. See [Your Game Index](/docs/features/refreshing-index). This tool remains in the repository for developers and for building indexes yourself.

Source: `binaries/AscendaraLocalRefresh/src/`

| File | Purpose |
| --- | --- |
| `AscendaraLocalRefresh.py` | CLI, progress tracking, atomic output swap |
| `base_scraper.py` | Base class for source scrapers |
| `steamrip_scraper.py` | SteamRIP scraper (default) |
| `goggames_scraper.py` | GOG-Games scraper |
| `utils.py` | Game ID encoding, blacklist, notifications |

## Features
- **Multiple sources**: `steamrip` (default) and `goggames`
- **Incremental refresh**: Only fetches SteamRIP posts modified since the existing index was built
- **Multi-threaded processing** with a configurable worker count
- **Cloudflare handling**: Detects whether Cloudflare is challenging requests and only asks for a `cf_clearance` cookie when needed
- **Real-time progress** in `progress.json`
- **Cover image downloads** with rate limiting, reusing covers the index already has
- **View counts** for popularity weighting, with a cache
- **Game ID encoding**: Converts numeric post IDs to 6-character IDs
- **Blacklist support**: Skips games blacklisted in Ascendara's settings
- **Safe output**: Writes new data to incoming files and swaps them in only after a successful run, with a backup to roll back to

## Usage

### Requirements
```bash
pip install -r binaries/AscendaraLocalRefresh/requirements.txt
# cloudscraper, requests, beautifulsoup4 (and win10toast on Windows)
```

### Command Line Arguments
```bash
python AscendaraLocalRefresh.py --output <dir> [--source steamrip|goggames] [--cookie <cf_clearance>] [--user-agent <ua>] [--workers N] [--per-page N] [--skip-views] [--full]
```

### Parameters
| Argument | Default | Description |
| --- | --- | --- |
| `--output`, `-o` | (required) | Output directory for `ascendara_games.json`, `imgs/`, and `progress.json` |
| `--source`, `-s` | `steamrip` | Source to scrape: `steamrip` or `goggames` |
| `--cookie`, `-c` | none | `cf_clearance` cookie value, without the `cf_clearance=` prefix. Only needed when Cloudflare is challenging requests. |
| `--user-agent`, `-u` | none | The User-Agent of the browser the cookie came from. Cloudflare cookies only work with the browser that solved the challenge. |
| `--workers`, `-w` | `8` | Number of worker threads |
| `--per-page`, `-p` | `100` | Posts fetched per API page (maximum 100) |
| `--skip-views` | off | Skip view counts (faster) |
| `--full` | off | Scrape everything instead of only what changed |
| `--extract-shared-index` with `--zip-path` | — | Legacy: extract a downloaded shared-index ZIP into `--output` |

### Example
```bash
python AscendaraLocalRefresh.py --output "C:/Ascendara/LocalIndex" --workers 8
```

If Cloudflare is active and no cookie was given, the tool prints `COOKIE_REFRESH_NEEDED` and waits for a cookie on standard input. The input can be the cookie value or a JSON object such as `{"cookie": "...", "userAgent": "..."}`. The same prompt appears if the cookie expires during a run.

## Workflow

### 1. Initialization
- Creates the output directory and an `imgs_incoming/` folder for new covers
- Starts `progress.json`
- Checks whether Cloudflare is challenging requests

### 2. Incremental or Full Refresh
For SteamRIP, if the output folder already has an index from the same source with at least 1,000 games that's less than 30 days old, the tool only fetches posts modified since that index was built (minus a 2-day margin, up to 1,500 posts) and merges them into the existing list. Otherwise, or with `--full`, it scrapes every post.

### 3. Post Processing
- Fetches category names from the WordPress API
- Processes posts with the worker threads
- Skips blacklisted games
- Extracts the name, size, version, release group, online/DLC flags, download links, and minimum requirements
- Downloads new cover images (0.15 seconds between downloads)
- Fetches view counts for popularity weighting
- Encodes post IDs into 6-character game IDs

### 4. Output
- Writes `ascendara_games_incoming.json`, then swaps it and the new covers into place, backing up the previous files until the swap succeeds
- Removes covers no longer referenced by any game
- Sets `hasIndexBefore` in `timestamp.ascendara.json`
- Shows a notification when the refresh completes

## Progress Tracking

The tool writes progress to `progress.json`:
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
  "timestamp": 1699999999.999,
  "waitingForCookie": false,
  "lastSuccessfulTimestamp": 1699990000
}
```

`errors` contains the 10 most recent errors.

### Status Values
- `initializing`: Setting up
- `running`: Actively processing
- `completed`: Finished successfully
- `failed`: Encountered a fatal error

### Phase Values
- `initializing`: Setting up the scraper
- `processing_posts`: Fetching and processing game posts
- `waiting_for_cookie`: Waiting for a new Cloudflare cookie
- `saving`: Writing the output file
- `swapping`: Moving the new files into place
- `done`: Finished

## Output Format

### Game Entry Structure
```json
{
  "game": "Game Name",
  "size": "12 GB",
  "version": "1.0.0",
  "releasedBy": "Release Group",
  "online": false,
  "dlc": true,
  "dirlink": "https://steamrip.com/game-name-free-download/",
  "download_links": {
    "gofile": ["https://gofile.io/d/..."],
    "buzzheavier": ["https://bzzhr.to/..."]
  },
  "weight": "15000",
  "imgID": "abc123xyz",
  "gameID": "AbCdEf",
  "category": ["Action", "Adventure"],
  "latest_update": "2026-01-15",
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

SteamRIP download links are grouped by provider: `gofile`, `qiwi`, `megadb`, `pixeldrain`, `buzzheavier`, `vikingfile`, `datanodes`, `1fichier`, and `fileditch`.

### Metadata Structure
```json
{
  "getDate": "January 15, 2026, 03:30 PM",
  "local": true,
  "source": "STEAMRIP",
  "listVersion": "1.0",
  "games": "3500",
  "lastScrape": "2026-01-15T15:30:00Z"
}
```

See the [game list format](/docs/api-reference/games-list) for every field.

## Game ID Encoding
Numeric post IDs are converted to 6-character IDs in base 46, after multiplying by a prime to spread out sequential IDs:

```python
GAME_ID_CHARS = "ABCDEFGHJKMNPQRSTUVWXYZabcdefghjkmnpqrstuvwxyz"  # 46 chars (no I, L, O, i, l, o)
GAME_ID_LENGTH = 6
SCRAMBLE_MULT = 2971
SCRAMBLE_MOD = 46 ** 6

def encode_game_id(post_id):
    scrambled = (int(post_id) * SCRAMBLE_MULT) % SCRAMBLE_MOD
    # convert to 6 base-46 digits

def decode_game_id(game_id):
    # convert from base 46, then multiply by the modular inverse
    inv = pow(SCRAMBLE_MULT, -1, SCRAMBLE_MOD)
    return str((num * inv) % SCRAMBLE_MOD)
```

## Blacklist Support
The tool reads `blacklistIDs` from Ascendara's `ascendarasettings.json` (`%APPDATA%\ascendara\`, `~/Library/Application Support/ascendara/`, or `~/.config/ascendara/`), decodes them to post IDs, and skips those posts.

## Error Handling
- API requests are retried up to 3 times on HTTP 429 and server errors, with increasing delays.
- If 5 cover downloads hit a Cloudflare challenge, the tool assumes the cookie expired and asks for a new one.
- A keep-alive thread visits the source every 30 seconds to keep the Cloudflare session active.
- Unhandled errors mark the run as `failed`, remove the incomplete incoming files, and open the [Crash Reporter](/docs/binary-tool/crash-reporter).

## Dependencies
- `cloudscraper`: Cloudflare-aware HTTP sessions
- `requests`: HTTP client
- `beautifulsoup4`: HTML parsing (GOG-Games)
- `win10toast` (Windows): Completion notifications
