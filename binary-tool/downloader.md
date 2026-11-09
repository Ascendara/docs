# Ascendara Downloader

Ascendara Downloader handles downloading, extracting, verifying, and installing games for Ascendara. One installation pipeline covers direct links, file hosts (Pixeldrain and Buzzheavier), Gofile folders, cloud-torrent files from debrid services, and magnet or `.torrent` links.

Source: `binaries/AscendaraDownloader/src/` (Rust, entry point `main.rs`). The downloader was rewritten from Python to Rust. It keeps the same command line, game files, and log, so the app works with it unchanged, and it picks up partial downloads left by the Python version.

**Gofile and torrent support are built in.** You don't need a separate Gofile helper, and the downloader itself downloads torrents rather than handing them to qBittorrent. The application starts the downloader and supplies the game information automatically.

## Using the Downloader in Ascendara

1. Choose a download directory in Ascendara's settings.
2. Open a game's download page, select an available provider, and choose your download options.
3. Start the download, or add it to your download queue.
4. Follow its progress in Ascendara. Downloading, extraction, and verification are separate stages; reaching 100% download progress does not mean installation is finished.
5. Wait for completion, then launch the game from your Library. If the detected executable is incorrect, select the correct one in the Executable Manager.

For Gofile, select its download option as usual. Ascendara resolves the sharing link, finds the files, and downloads them through the same binary. A folder containing several files is processed as one game download.

The download speed limit follows Ascendara's settings. Notifications and actions after completion, such as locking, sleeping, or shutting down the computer, also use the application's settings. The downloader keeps the computer from sleeping while it runs.

### Progress and Disk Space

During transfer, Ascendara reports the downloaded percentage, speed, and estimated time remaining. Speed is measured over the last few seconds, and the time remaining is smoothed so a brief dip barely moves it. If the server does not report a total size, the downloader reports the amount downloaded instead of a percentage or time remaining. Gofile and cloud-torrent progress combines every file in the download.

During extraction, progress includes the current file, the extracted file count, an overall percentage across every archive, and read and write speeds. Extraction progress never moves backwards. When a large block is being decompressed in memory, or the drive is still writing out data it was given, Ascendara shows that instead of reporting a stall.

Leave room for both the downloaded archives and the extracted game. Updates can also temporarily keep files being replaced, and archive repair needs space for a fresh copy of the affected archive. The downloader checks free space before the download (using the listed size), before each transfer, and before each archive is extracted.

### Stopping, Resuming, and Retrying

Use Ascendara's download controls to stop or resume a download. Keep its partial files if you intend to resume; choosing an option that deletes the files removes the data needed for recovery.

HTTP downloads resume where they stopped when the server supports byte ranges. The downloader saves its progress for every connection, and only after the data it describes has reached the disk. If the remote file changed, the server stopped honoring ranges, or the saved progress doesn't match, the transfer starts over. Completed Gofile and cloud-torrent files are reused when their saved receipt and file size match the current source. Torrents resume by rechecking the pieces already on disk.

Interrupted extraction restarts in a fresh staging folder. It does not resume at an arbitrary point inside the archive. Downloaded source files are kept when extraction or verification fails, so Ascendara can retry extraction without downloading again.

## How Installation Works

1. **Resolve the source.** Direct HTTP or HTTPS links provide the file. Pixeldrain viewer links are turned into download links, and Buzzheavier pages have their download links resolved. Gofile sharing links are resolved into file listings and authenticated download links. Magnet and `.torrent` links go to the built-in BitTorrent client.
2. **Transfer the files.** Downloads stream to disk over several connections, with progress reporting, bounded retries, optional speed limiting, and resume. Where the host publishes a checksum (Pixeldrain's SHA-256 or Gofile's MD5), the finished file is checked against it.
3. **Name the download by its contents.** The downloader identifies ZIP, RAR, 7z, and executable files from their bytes, not their names, and renames the file to match.
4. **Prepare a staging folder.** Archives are extracted into a temporary folder inside the game's directory. Loose files from Gofile, cloud torrents, and torrents are linked or copied into the same staged payload.
5. **Check the extracted output.** The downloader compares the expected files and sizes with the staged output before installing it.
6. **Install the payload.** Files move into the game directory. Existing files being replaced are first moved into a rollback folder.
7. **Verify the installation.** Installed files are checked against the file manifest, and the downloader finds the game's executable.
8. **Finish and clean up.** Archives and temporary download files that were used are deleted, and the game's size is updated to what the installation actually occupies. Completion is published before any post-download action runs.

ZIP, RAR, and 7z archives are supported, including split sets such as `game.part1.rar`, `game.r00`, and `game.7z.001`. Extraction runs inside the downloader, with no external 7-Zip or UnRAR program. Executable downloads are copied into the installation rather than run as installers. Split archives need every volume, including the first. Archives at the top level of an extracted payload are extracted in turn; archives inside game asset folders are left as they are.

The downloader leaves out `.url` shortcuts and `_CommonRedist` content. It removes enclosing wrapper folders (such as `Game/` or `Game v1.2/`) so the game sits at the top of its folder, but keeps folders the game needs by name, such as `bin`, `data`, or `*_Data`.

For torrents and cloud torrents on Windows, if the download contains a setup program instead of a ready-to-run game, Ascendara asks you to run the installer and confirm when it's done.

### Recovery and Verification Limits

When extraction fails, the downloader first decides why:

- **Damaged archive:** The archive is downloaded again, replacing only the volumes whose bytes changed, then extracted again. If the file already matched the host's checksum, or a fresh download returns identical bytes, the downloader reports that the archive is damaged at the source and another source is needed.
- **Blocked file, security software, or an unknown error:** The downloader retries once quietly after a short pause, because file locks and security scans are often gone a moment later.
- **Otherwise,** or if the automatic step didn't help, Ascendara asks you whether to retry extraction or cancel. Cancelling keeps the downloaded files.

Missing or incomplete extracted files get the same prompt once. If they're still missing after the retry, the downloader stops and keeps the source files.

If installation fails before verification, the downloader restores the overwritten files and the previous file manifest. If restoring fails, the staging folder is kept with its recovery files. Forced process termination or power loss can interrupt this, so automatic rollback is not guaranteed in those cases.

Installed-file verification checks that every expected file exists, is a regular file, and has the expected size. Archive decoders check each file's CRC as they extract. This is not a malware scan. When the only missing files are DLLs listed in an online-fix package's `dlllist.txt` (files security software often removes), a fresh installation is kept so Ascendara can repair just those files.

## Integrated Gofile Support

The downloader accepts Gofile sharing links in the form `https://gofile.io/d/CONTENT_ID`. It creates a temporary account token automatically, resolves files in the folder and its subfolders, and downloads them with that token. The token is only sent to Gofile's own domains. Users do not need a Gofile account.

Files download one after another, then archives and loose files enter the shared installation pipeline. Files from Gofile subfolders keep their relative paths. Duplicate names and names that conflict with Ascendara's own files are rejected instead of silently overwriting each other. Empty folders, inaccessible content, and missing archive volumes produce errors. If a file fails Gofile's checksum, it's downloaded once more before the downloader reports it as damaged at the source.

Developers can supply `--password` for a password-protected Gofile folder. **This is the folder password, not an archive password.** Archives are tried without a password first, then with the built-in password `steamrip.com`; the CLI has no separate archive-password option.

## Torrents

Magnet links and links to `.torrent` files are downloaded by a built-in BitTorrent client (using DHT to find peers), into `.ascendara-downloads/torrent-files/`. The speed limit from Ascendara's settings applies. The files then go through the same extraction, installation, and verification steps as any other download. If no peer sends data for 30 minutes, the download fails with a message suggesting you try later or choose another source.

When a [debrid service](/docs/downloads/debrid-services) downloads a torrent in its cloud, Ascendara writes the list of finished files to a JSON file and starts the downloader with `--file-list`. The downloader then fetches those files over HTTP like a Gofile folder.

## Command-Line Usage

Most users should let Ascendara run the downloader. Developers can run the binary directly with the following argument order:

```text
AscendaraDownloader URL GAME ONLINE DLC IS_VR UPDATE_FLOW VERSION SIZE DOWNLOAD_DIR [GAME_ID] [--provider auto|direct|gofile] [--password PASSWORD] [--file-list] [--withNotification THEME]
AscendaraDownloader --retry-extraction GAME_JSON
```

| Argument | Meaning |
| --- | --- |
| `URL` | Direct download URL, Pixeldrain or Buzzheavier link, Gofile sharing URL, magnet link, or `.torrent` URL. With `--file-list`, the path to a cloud-torrent file list. |
| `GAME` | Game name; a sanitized form is used for the installation folder and metadata filename. |
| `ONLINE` | Online-support metadata flag. |
| `DLC` | DLC metadata flag. |
| `IS_VR` | VR metadata flag. |
| `UPDATE_FLOW` | Whether to update an existing installation and reuse its metadata. |
| `VERSION` | Version string to record for this download. |
| `SIZE` | Human-readable size, such as `"20 GB"`; used for metadata and the first free-space check. |
| `DOWNLOAD_DIR` | Parent directory for games. The downloader creates the game subfolder itself. |
| `GAME_ID` | Optional catalog identifier; defaults to an empty string. |
| `--provider` | Defaults to `auto`, which recognizes Gofile and Buzzheavier links. `gofile` forces the Gofile resolver; `direct` downloads the URL as given. |
| `--password` | Optional password for a Gofile folder. |
| `--file-list` | Treats `URL` as a JSON file listing cloud-torrent files (`transferId` and `files` with `filename`, `url`, and optional `size`). |
| `--withNotification` | Shows notifications through the [Notification Helper](/docs/binary-tool/notification-helper) with the given Ascendara theme. |
| `--retry-extraction` | Re-runs extraction and installation from the files already on disk, without network access. Takes the path to the game's `.ascendara.json`. |

Boolean arguments accept `true`/`false`, `1`/`0`, or `yes`/`no`, ignoring case. Quote names, paths, and sizes containing spaces. The old qBittorrent options (`--torrentClient`, `--qbitHost`, `--qbitPort`, `--qbitUsername`, `--qbitPassword`) are still accepted and ignored.

On Windows the downloader is built without a console window, so it writes nothing to the terminal; follow progress in the game's JSON file and the log. On Linux and macOS, log lines are also printed to the terminal.

The following PowerShell examples use placeholder URLs; replace them with your actual source:

```powershell
# Direct download into C:\Games\Example Game
.\AscendaraDownloader.exe "https://example.com/game.zip" "Example Game" false false false false "1.0" "20 GB" "C:\Games"

# Gofile folder, with optional folder password
.\AscendaraDownloader.exe "https://gofile.io/d/CONTENT_ID" "Example Game" false false false false "1.0" "20 GB" "C:\Games" --provider gofile --password "folder-password"

# Magnet link
.\AscendaraDownloader.exe "magnet:?xt=urn:btih:..." "Example Game" false false false false "1.0" "20 GB" "C:\Games"

# Update an existing game in the same parent directory
.\AscendaraDownloader.exe "https://example.com/update.zip" "Example Game" false false false true "1.1" "2 GB" "C:\Games"

# Retry extraction from the files already downloaded
.\AscendaraDownloader.exe --retry-extraction "C:\Games\Example Game\Example Game.ascendara.json"
```

`UPDATE_FLOW` installs the supplied payload; it does not find or download an update by itself. The installed version stays the same (the new one is kept in `pendingVersion`) until the update is verified. Existing manifest entries are merged with the new payload, and files absent from the update are not deleted. The application supplies the update link and target version.

## Developer Integration

### Source Layout

All of these modules are in `binaries/AscendaraDownloader/src`:

| File | Responsibility |
| --- | --- |
| `main.rs` | Command line, logging, crash reporter hook, and keeping the computer awake. |
| `pipeline.rs` | One download from link to installed game: provider dispatch, progress, staging, extraction recovery, cleanup, and `--retry-extraction`. |
| `http.rs` | Resumable multi-connection HTTP transfers, range validation, retries, speed limiting, and checksum checks. |
| `providers.rs` | Link validation and resolving Pixeldrain, Buzzheavier, Gofile, and cloud-torrent file lists. |
| `torrent.rs` | The built-in BitTorrent client for magnet and `.torrent` links. |
| `archive.rs` | Archive detection, listing, split volumes, and extraction (ZIP, RAR, 7z). |
| `install.rs` | Wrapper-folder removal, installation with a rollback journal, verification, and executable detection. |
| `state.rs` | Reading and writing the game's `.ascendara.json`, stop requests, recovery prompts, and progress meters. |
| `system.rs` | App settings, notifications, the crash reporter, sleep prevention, and post-download actions. |
| `util.rs` | Sizes, safe file and archive member names, disk space, file moves with retries, and logging. |

Electron's `electron/modules/downloads/downloads.js` starts the downloader with the positional arguments above and reads the game's metadata for progress. In development, Ascendara runs the Cargo build from `binaries/AscendaraDownloader/target/release/`.

### State and Files

For a game installed at `DOWNLOAD_DIR/GAME`, the downloader uses:

| Path | Purpose |
| --- | --- |
| `GAME.ascendara.json` | Game metadata and the `downloadingData` progress and error object. |
| `filemap.ascendara.json` | Installed-file manifest mapping relative paths to expected sizes. |
| `.ascendara-downloads/` | Downloaded sources, partial transfers, and Gofile, torrent, and cloud-torrent folders. |
| `*.ascpart` and `*.ascpart.json` beside a download | Partial data and its resume state: URL, total size, validator, and each connection's position. |
| `.ascendara-receipts/` inside a Gofile or cloud-torrent folder | Receipts for completed files. |
| `.ascendara-stage-*/` | Temporary payload and rollback data. |

The game's JSON file is the only channel between the downloader and the app. `downloadingData` exposes `downloading`, `extracting`, `verifying`, and `updating` flags. Transfer fields include `progressCompleted`, `progressDownloadSpeeds`, `timeUntilComplete`, and `lastProgress`. `extractionProgress` contains `currentFile`, `filesExtracted`, `totalFiles`, `percentComplete`, `extractionSpeed`, `readSpeed`, `writeSpeed`, `decompressing`, and `waitingOnDrive`. Retries can set `retryAttempt` and `autoRecovery`. Failures set `error` and `message` (and `previousError` after a retried extraction), or `verifyError` for installed-file verification errors.

The app requests actions by writing to the same file:

- **Stop:** Set `downloadingData.stopped` to `true`, or remove `downloadingData` (or the file) to remove the download. The downloader checks for this several times a second and stops cooperatively, keeping its partial files.
- **Recovery prompts:** When the downloader needs a decision, it sets `awaitingRecoveryAction` and a `recoverableError` with a `requestId`. The app answers by setting `recoveryAction` to `retry` or `cancel`.

Successful completion removes `downloadingData`. The downloader writes the file atomically and never overwrites a stop request. Integrations should read the final metadata rather than the exit code, because download errors are recorded in the file and the process still exits normally.

### Transfer and Extraction Design

Each file is split into pieces of 4 to 64 MB that connections claim in order. When no unclaimed piece is left, an idle connection splits the slowest active piece, so one slow connection doesn't hold up the end of a download. The number of connections comes from `threadCount` (default 8, up to 32). **Single Stream Download** limits each file to one connection, but only if you turned it on yourself. Hosts that ignore byte ranges are downloaded over a single connection.

Requests use `Range` and `If-Range` with a strong ETag or Last-Modified validator, and every response is checked against the expected range, size, and validator before its bytes are kept. A response that is actually an HTML error page is detected and rejected. Connections that receive nothing for 30 seconds reconnect. HTTP 408, 429, and server errors are retried, honoring `Retry-After` up to two minutes. Other client errors, such as 403 or 404, fail immediately.

Archives are extracted inside the downloader on a background thread: ZIP with the `zip` crate, 7z with `sevenz-rust2`, and RAR with the UnRAR library. Archive member names are checked so they can't escape the game folder or use names Windows can't create, links inside archives are skipped, and payload files can't overwrite Ascendara's own files.

Installation stages files on the destination drive and moves them into place, retrying when security software or the search indexer briefly holds a file open. Rollback stores overwritten files under numbered names in the stage's `rollback/` folder, with `paths.json` mapping them to their original paths and `previous-filemap.json` keeping the old manifest. Recovery files kept after an interrupted installation should be inspected before removal; they are not automatically replayed on startup.

### Building

```bash
cd binaries/AscendaraDownloader
cargo build --release --locked
```

The executable is written to `target/release/AscendaraDownloader(.exe)`, where `package.json` and development mode expect it. Rust 1.90 or newer is required. On Windows, `build.rs` embeds the icon. See [Creating the Tools](/docs/developer/creating-the-tools).

Settings come from `ascendarasettings.json` in Ascendara's application-data directory: `%APPDATA%\Ascendara\` on Windows, `~/Library/Application Support/ascendara/` on macOS, and `~/.config/ascendara/` on Linux, falling back to the `Electron` folder that development builds use. The `downloadLimit` setting is in KB/s (`0` means unlimited). `singleStream`, `singleStreamChosen`, and `threadCount` control connections per file, and `behaviorAfterDownload` selects the completion action. Direct CLI runs also read these settings.

## Troubleshooting

| Problem | What to do |
| --- | --- |
| Expired or unavailable link, including HTTP 403, 404, or 410 | Refresh the game source or choose another download link. |
| Repeated connection failures or provider rate limits | Let the automatic retries finish. If they run out, try again later or use another provider. |
| Gofile password, private-content, or empty-folder error | Confirm the sharing link is accessible and contains files. For CLI use, supply the correct folder password with `--password`. |
| Torrent finds no peers | Wait, or choose another source. The download fails after 30 minutes without data. |
| Insufficient disk space | Free space on the destination drive for downloads, extraction, and any repair or rollback files, then retry. |
| Missing first archive volume or repeated damage | Use a source with the complete archive set. Downloading identical damaged bytes again won't repair it. |
| Extraction fails or produces incomplete files | Let the automatic recovery finish, then choose **Retry** when asked. If it fails again, check the log and your security software's history. |
| Files disappear or stay locked during installation | Check your security software's history and other programs using the game folder. Close the game before updating. |
| Verification or rollback failure | Review the reported files and the log. Keep any remaining staging or rollback folder until the problem is resolved. |

The downloader log is `downloadmanager.log` at:

- **Windows:** `%APPDATA%\Ascendara by tagoWorks\downloadmanager.log`
- **macOS/Linux:** `~/.config/Ascendara by tagoWorks/downloadmanager.log`

When reporting an issue, include the stage that failed, the error message, and the relevant part of the log. Remove private download links or credentials before sharing logs.
