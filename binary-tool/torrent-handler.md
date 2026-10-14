# Ascendara Torrent Handler

## Overview
The Torrent Handler downloads games from magnet links through qBittorrent and installs them. It adds the torrent to qBittorrent through the Web UI API, reports progress to Ascendara, extracts the downloaded archives, and verifies the installed files. If the torrent contains an installer instead of archives, it hands off to a manual installation step.

Source: `binaries/AscendaraTorrentHandler/src/AscendaraTorrentHandler.py`

> **Note:** If you use [TorBox](/docs/features/torbox-integration) with **Use TorBox for torrents** on, magnet links are sent to TorBox instead and this tool isn't used (unless TorBox fails and **Fall back to qBittorrent** is on).

## Features
- Adds magnet links to qBittorrent with sequential downloading
- Real-time progress, speed, and ETA in the game's JSON file
- Disk space checks before downloading and extracting
- Archive extraction (ZIP, RAR, 7z) with progress, using 7-Zip when available
- Falls back to a manual installer flow when the torrent contains a setup program
- Executable detection, file map creation, and installed-file verification
- Desktop notifications through the [Notification Helper](/docs/binary-tool/notification-helper)
- Removes incomplete torrents and their data if the handler exits early
- Crash Reporter integration

## Prerequisites
- **qBittorrent** installed and running with the **Web UI** enabled (default port `8080`)
- Web UI credentials that match Ascendara's **qBittorrent Configuration** in Settings (default `admin` / `adminadmin`)
- Torrenting enabled in Ascendara (**Settings** → **Torrent on Ascendara**)
- A VPN is strongly recommended, because peers can see your IP address

The Torrent Handler is included with Ascendara. On Windows, if it's missing, Ascendara offers to install it as a sidecar when you enable torrenting.

## Usage

### Command Line Arguments
```bash
AscendaraTorrentHandler <magnet> <game> <online> <dlc> <isVr> <updateFlow> <version> <size> <dir> [--withNotification <theme>] [--qbitHost <host>] [--qbitPort <port>] [--qbitUsername <user>] [--qbitPassword <pass>]
```

### Parameters
| Argument | Description |
| --- | --- |
| `magnet` | Magnet link for the torrent |
| `game` | Game name; used for the game folder and metadata file |
| `online`, `dlc`, `isVr` | Metadata flags (`true`/`false`) |
| `updateFlow` | Whether this updates an existing installation (`true`/`false`) |
| `version` | Version string to record |
| `size` | Human-readable size, such as `"50 GB"`; used for metadata and disk space checks |
| `dir` | Parent download directory. The handler creates the game folder itself. |
| `--withNotification` | Show notifications with the given theme (`light`, `dark`, `blue`, …) |
| `--qbitHost` | qBittorrent Web UI host (default `localhost`) |
| `--qbitPort` | qBittorrent Web UI port (default `8080`) |
| `--qbitUsername` | qBittorrent Web UI username (default `admin`) |
| `--qbitPassword` | qBittorrent Web UI password (default `adminadmin`) |

### Example
```bash
AscendaraTorrentHandler.exe "magnet:?xt=urn:btih:..." "MyGame" true false false false "1.0.0" "50 GB" "C:/Games" --withNotification emerald --qbitHost 127.0.0.1 --qbitPort 8080
```

## How It Works

1. **Prepare**: Creates the game folder and `{game}.ascendara.json`, and checks that there's enough free space for the download plus extraction.
2. **Add the torrent**: Connects to qBittorrent and adds the magnet with the game folder as the save path and sequential downloading enabled.
3. **Download**: Writes progress to `downloadingData`. While qBittorrent is still looking for peers, `waiting` is `true`, and Ascendara shows **Waiting for Peers**.
4. **Install**:
   - If the download contains archives, the handler extracts them into the game folder (largest first, skipping continuation parts of multi-volume RAR sets), deletes each archive after it's extracted, and flattens a single wrapper folder.
   - If there are no archives, it looks for an installer (an `.exe` starting with `setup` or the game's name). It then sets `pendingManualInstall` with the installer path, and Ascendara asks the user to run the installer as administrator and confirm when it's done.
5. **Finish**: Detects the game's executable, creates `filemap.ascendara.json`, removes junk files, verifies the installed files, and removes `downloadingData` on success. Verification errors are kept in `verifyError` so Ascendara can show them.

### Game State
```json
{
  "game": "MyGame",
  "online": true,
  "dlc": false,
  "isVr": false,
  "version": "1.0.0",
  "size": "50 GB",
  "executable": "C:/Games/MyGame/MyGame/game.exe",
  "downloadingData": {
    "downloading": true,
    "waiting": false,
    "extracting": false,
    "verifying": false,
    "updating": false,
    "progressCompleted": "42.10",
    "progressDownloadSpeeds": "12.40 MB/s",
    "timeUntilComplete": "8m 12s",
    "extractionProgress": {
      "currentFile": "",
      "filesExtracted": 0,
      "totalFiles": 0,
      "percentComplete": 0,
      "extractionSpeed": "0 files/s"
    }
  }
}
```

On failure, `downloadingData` contains `error: true` and a `message`, and keeps the progress reached so far.

## Logging
The handler logs to the shared download log, `downloadmanager.log` (see [Common Issues](/docs/troubleshooting/common-issues#log-files) for its location), and writes a detailed log to the system temp folder as `ascendara_torrent_<timestamp>.log`.

## Troubleshooting

### qBittorrent Connection Failed
1. Make sure qBittorrent is running and the Web UI is enabled.
2. Check that the host, port, username, and password in Ascendara's settings match qBittorrent.
3. If the credentials are correct, enable **Bypass authentication for clients on localhost** in qBittorrent's Web UI settings, and add `127.0.0.1/32` to the whitelisted subnets.
4. Make sure a VPN, proxy, or network tool isn't rerouting localhost traffic.

### Installation Errors
- Check that you have write permissions in the download directory.
- Make sure there's enough free space for the download and extraction.
- If the torrent contains an installer, run it as administrator and set its destination to the folder Ascendara shows.

### Error Codes
The handler passes the generic code `1` with the error message to the [Crash Reporter](/docs/binary-tool/crash-reporter) for unhandled errors (connection, download, installation, or argument errors).
