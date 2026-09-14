# Ascendara Downloader

Ascendara Downloader handles downloading, extracting, verifying, and installing games for Ascendara. The rewritten V4 downloader uses one installation pipeline for direct downloads and Gofile content.

**Gofile support is built into the downloader.** You do not need to install or run a separate Gofile Helper. The application starts the downloader and supplies the game information automatically.

## Using the Downloader in Ascendara

1. Choose a download directory in Ascendara's settings.
2. Open a game's download page, select an available provider, and choose your download options.
3. Start the download, or add it to your download queue.
4. Follow its progress in Ascendara. Downloading, extraction, and verification are separate stages; reaching 100% download progress does not mean installation is finished.
5. Wait for completion, then launch the game from your Library. If the detected executable is incorrect, select the correct one in the Executable Manager.

For Gofile, select its download option as usual. Ascendara resolves the sharing link, finds the files, and downloads them through the same binary. A folder containing several files is processed as one game download.

The download speed limit follows Ascendara's settings. Notifications and supported actions after completion, such as locking, sleeping, or shutting down the computer, also use the application's settings.

### Progress and Disk Space

During transfer, Ascendara reports the downloaded percentage, speed, and estimated time remaining. If the server does not report a total size, the downloader reports the amount downloaded instead of a reliable percentage or ETA. Gofile progress combines the files in the folder.

During extraction, progress includes the current file, extracted file count, percentage, and extraction speed. Large files can take time even when the completed file count stays unchanged.

Leave room for both the downloaded archives and the extracted game. Updates can also temporarily retain files being replaced, and archive repair needs space for a fresh copy of the affected archive. The downloader checks space during transfer and extraction; the game's listed size alone is not a guarantee of the total space needed.

### Stopping, Resuming, and Retrying

Use Ascendara's download controls to stop or resume a download. Keep its partial files if you intend to resume; choosing an option that deletes the files removes the data needed for recovery.

The downloader can reuse partial HTTP transfers when the server supports byte ranges and supplies a usable file validator. If the remote file changes, the URL changes, or the server cannot safely resume, the transfer may restart from the beginning. Completed Gofile files can be reused when their saved receipt and file size match the current source information.

Interrupted extraction restarts in a fresh staging folder. It does not resume at an arbitrary point inside the archive. Downloaded source files are retained on extraction or verification failure so they remain available for recovery.

## How Installation Works

1. **Resolve the source.** Direct HTTP or HTTPS links provide the file. Gofile sharing links are resolved into file listings and authenticated download links. Supported Buzzheavier-style pages have their download links resolved internally.
2. **Transfer the files.** Downloads stream to disk with progress reporting, bounded retries, optional speed limiting, and resumable partial data where supported.
3. **Prepare a staging folder.** Archives are extracted into a temporary folder inside the game's directory. Gofile loose files are copied into the same staged payload.
4. **Check the extracted output.** The downloader compares expected files and sizes with the staged output before installing it.
5. **Install the payload.** Files move into the game directory. Existing files being replaced are first moved into a rollback folder.
6. **Verify the installation.** Installed files are checked against the file manifest, and the downloader attempts to identify the game's executable.
7. **Finish and clean up.** Successfully consumed archives and temporary download files are cleaned up. Completion is published before configured post-download actions run.

ZIP, RAR, and 7z archives are supported. Executable downloads are copied into the installation rather than automatically executed as installers. Multipart RAR sets require all necessary volumes, including the first volume. Root-level continuation archives can be unpacked automatically; archives nested inside game asset folders are left intact.

The downloader filters `.url` shortcuts and `_CommonRedist` content. It can remove a single enclosing game folder when the payload matches its wrapper-detection rules, avoiding unnecessary nesting in the installation.

### Recovery and Verification Limits

Incomplete extracted output gets one automatic retry in a fresh staging folder. Recognized archive integrity errors can trigger one repair download of the affected archive or volume set, followed by another extraction attempt. If the source returns identical damaged bytes, the downloader reports that another source is needed.

Handled installation failures trigger an attempt to restore overwritten files and the previous file manifest. If rollback fails, the staging folder is retained with recovery information. Forced process termination or power loss can interrupt this process; automatic rollback is not guaranteed in those cases.

Installed-file verification checks that expected files exist, are regular files, and have the expected sizes. Archive decoders also perform their own integrity checks. This is not a full cryptographic verification of every installed file or a malware scan.

## Integrated Gofile Support

The downloader accepts Gofile sharing links in the form `https://gofile.io/d/CONTENT_ID`. It creates a temporary API account token automatically, resolves files in the folder and its subfolders, and uses an authenticated session for downloads. Users do not need to provide a Gofile account for this flow.

Files are downloaded sequentially, then archives and loose files enter the shared installation pipeline. Files from Gofile subfolders are combined at the download root. Duplicate filenames from different folders are rejected instead of silently overwriting each other. Empty folders, incomplete listings, inaccessible content, and missing archive volumes produce errors.

Developers can supply `--password` for a password-protected Gofile folder. **This is the folder password, not an archive extraction password.** Archive extraction currently uses the built-in password `steamrip.com`; the CLI does not expose a separate archive-password option.

## Command-Line Usage

Most users should let Ascendara run the downloader. Developers can invoke the binary or Python entry point directly with the following argument order:

```text
AscendaraDownloader.exe URL GAME ONLINE DLC IS_VR UPDATE_FLOW VERSION SIZE DOWNLOAD_DIR [GAME_ID] [--provider auto|direct|gofile] [--password PASSWORD] [--withNotification THEME]
```

| Argument | Meaning |
| --- | --- |
| `URL` | Direct download URL, supported provider page, or Gofile sharing URL. |
| `GAME` | Game name; a sanitized form is used for the installation folder and metadata filename. |
| `ONLINE` | Online-support metadata flag. |
| `DLC` | DLC metadata flag. |
| `IS_VR` | VR metadata flag. |
| `UPDATE_FLOW` | Whether to update an existing installation and reuse its metadata. |
| `VERSION` | Version string to record for this download. |
| `SIZE` | Human-readable size, such as `"20 GB"`; used for metadata and preliminary space estimation. |
| `DOWNLOAD_DIR` | Parent directory for games. The downloader creates the game subfolder itself. |
| `GAME_ID` | Optional catalog identifier; defaults to an empty string. |
| `--provider` | Defaults to `auto`, which recognizes Gofile sharing hosts. `gofile` explicitly selects that resolver; `direct` bypasses automatic Gofile selection. |
| `--password` | Optional password for Gofile folder discovery. |
| `--withNotification` | Enables notification-helper calls using the supplied Ascendara theme. |

Boolean arguments accept `true`/`false`, `1`/`0`, or `yes`/`no`, ignoring case. Quote names, paths, and sizes containing spaces.

The following PowerShell examples use placeholder URLs; replace them with your actual source:

```powershell
# Direct download into C:\Games\Example Game
.\AscendaraDownloader.exe "https://example.com/game.zip" "Example Game" false false false false "1.0" "20 GB" "C:\Games"

# Gofile folder, with optional folder password
.\AscendaraDownloader.exe "https://gofile.io/d/CONTENT_ID" "Example Game" false false false false "1.0" "20 GB" "C:\Games" --provider gofile --password "folder-password"

# Update an existing game in the same parent directory
.\AscendaraDownloader.exe "https://example.com/update.zip" "Example Game" false false false true "1.1" "2 GB" "C:\Games"

# Inspect arguments from the source directory
python src/AscendaraDownloader.py --help
```

`UPDATE_FLOW` installs the supplied payload; it does not discover or download an update by itself. Existing manifest entries are merged with the new payload, and files absent from the update are not automatically deleted. The application supplies the update link and target version.

## Developer Integration

### Source Layout

All of these modules belong to `binaries/AscendaraDownloader/src` and are packaged into the downloader:

| File | Responsibility |
| --- | --- |
| `AscendaraDownloader.py` | CLI, game metadata, provider dispatch, progress, extraction supervision, staging, installation, verification, and executable detection. |
| `AscendaraTransfer.py` | Streaming HTTP transfers, range validation, retry limits, speed limiting, and partial-download metadata. |
| `AscendaraGofile.py` | Gofile discovery, folder traversal, authentication, and provider errors. It does not launch a separate helper. |
| `AscendaraDownloadRecovery.py` | Retry timing, response-size validation, archive-family matching, and repair downloads. |
| `AscendaraRarRecovery.py` | Native UnRAR bindings and shared archive-path and recovery utilities. |

`AscendaraDownloader_OLD.py` is not the current entry point. Use `AscendaraDownloader.py` and the downloader build specification when developing or packaging V4.

Electron's `electron/modules/downloads.js` starts the downloader with the positional arguments above and reads game metadata for progress. Torrent and magnet links use the separate [Torrent Handler](/docs/binary-tool/torrent-handler).

### State and Files

For a game installed at `DOWNLOAD_DIR/GAME`, the downloader uses:

| Path | Purpose |
| --- | --- |
| `GAME.ascendara.json` | Game metadata and the `downloadingData` progress/error object. |
| `filemap.ascendara.json` | Installed-file manifest mapping relative paths to expected sizes. |
| `.ascendara-downloads/` | Downloaded sources, partial transfers, and Gofile download folders. |
| `*.resume.json` beside a partial file | URL, remote validator, and total-size information for safe HTTP resumption. |
| `.ascendara-receipts/` inside a Gofile download folder | Receipts for completed Gofile files. |
| `.ascendara-stage-*/` | Temporary payload, worker manifest, and rollback data. |

`downloadingData` exposes `downloading`, `extracting`, `verifying`, and `updating` flags. Transfer fields include `progressCompleted`, `progressDownloadSpeeds`, and `timeUntilComplete`. `extractionProgress` contains `currentFile`, `filesExtracted`, `totalFiles`, `percentComplete`, and `extractionSpeed`. Retries can set `retryAttempt`; failures use `error` and `message`, or `verifyError` for installed-file verification errors.

Successful completion removes `downloadingData`. The downloader writes metadata atomically and preserves a stop request set by Electron. Setting `downloadingData.stopped` requests cooperative cancellation; the application's stop flow can also terminate processes. Integrations should inspect the final metadata rather than treating process exit code zero as proof of success, because routine pipeline errors can be recorded without being re-raised by the CLI.

### Transfer and Extraction Design

Transfers use `Range` and `If-Range` with a strong ETag or Last-Modified validator. The downloader validates returned ranges and lengths before appending data. Network failures use bounded backoff; HTTP 429 and 503 can honor `Retry-After`. Most other HTTP client errors fail immediately. A transfer permits up to 48 attempts, with a six-failure limit that resets after substantial forward progress.

Archive decoding runs in a spawned worker process. The parent receives progress through a pipe, handles cancellation, and stops workers that exit unexpectedly or report no decoder activity for 300 seconds. The worker does not write frontend progress JSON directly.

ZIP extraction uses Python's `zipfile`; Windows RAR extraction uses `UnRAR.dll`; 7z and non-Windows RAR extraction require an available 7-Zip command-line tool. Archive paths are checked against the extraction root, links are rejected, and payload files cannot overwrite reserved Ascendara metadata.

Installation stages files on the destination volume and moves them into place with bounded retries for transient Windows file locks. Rollback stores overwritten files under numbered names, with `rollback/paths.json` mapping them to their original relative paths and `previous-filemap.json` retaining the old manifest when one existed. Recovery files retained after an interrupted installation should be inspected before removal; they are not automatically replayed on startup.

### Running and Packaging from Source

From `binaries/AscendaraDownloader`, use a Python environment with the repository requirements:

```powershell
python -m pip install -r requirements.txt
python src/AscendaraDownloader.py --help
```

For the Windows binary, install PyInstaller in your build environment and use the existing specification:

```powershell
python -m pip install pyinstaller
python -m PyInstaller AscendaraDownloader.spec
```

The specification builds `dist/AscendaraDownloader.exe`, includes `src/UnRAR.dll`, and uses `src/ascendara.ico`. Keep the sibling Python modules available when running from source. Packaging everything into one downloader does not remove native extraction dependencies: provide a discoverable `7z`, `7zz`, or `7za` tool for formats that need it. The current specification bundles UnRAR but does not explicitly bundle 7-Zip.

Settings come from `ascendarasettings.json` in Ascendara's platform-specific application-data directory. On Windows, a frozen build reads the `Ascendara` folder, while source execution reads `Electron`. The `downloadLimit` setting is interpreted in KiB/s (`0` means unlimited). `behaviorAfterDownload` selects the completion action; direct CLI runs also read these settings.

## Troubleshooting

| Problem | What to do |
| --- | --- |
| Expired or unavailable link, including HTTP 403, 404, or 410 | Refresh the game source or choose another download link. |
| Repeated connection failures or provider rate limits | Allow automatic retries to finish. If they are exhausted, try again later or use another available provider. |
| Gofile password, private-content, or empty-folder error | Confirm the sharing link is accessible and contains files. For CLI use, supply the correct folder password with `--password`. |
| Insufficient disk space | Free space on the destination drive for downloads, extraction, and any repair or rollback files, then retry. |
| Missing first RAR volume or repeated integrity failure | Use a source containing the complete archive set. Identical damaged bytes cannot be repaired by repeatedly downloading that source. |
| Extraction stalls or produces incomplete files | Let the bounded recovery attempt finish. If it fails again, inspect the log and retained source files. |
| Missing 7-Zip tool or UnRAR library | Check the downloader's native dependencies, especially when running a development build. |
| Files disappear or remain locked during installation | Check security-software history and other processes using the game folder. Close the game before updating and inspect the reported file path. |
| Verification or rollback failure | Review the reported files and log. Preserve any retained staging/rollback folder until recovery is resolved. |

The downloader log is `downloadmanager.log` at:

- **Windows:** `%APPDATA%\Ascendara by tagoWorks\downloadmanager.log`
- **macOS/Linux:** `~/.config/Ascendara by tagoWorks/downloadmanager.log`

When reporting an issue, include the stage that failed, the error message, and the relevant log excerpt. Remove private download links or credentials before sharing logs.
