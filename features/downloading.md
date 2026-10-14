# Downloading Games

Ascendara downloads, extracts, verifies, and installs games for you. This page explains every way to start a download, how to follow and control downloads, and the download settings. For the technical details of the download pipeline, see the [Download Manager](/docs/binary-tool/downloader) binary documentation.

## Before You Start

Choose a download directory during setup or in **Settings** → **Download Manager**. You can add more directories under **Additional Locations**. When more than one directory is set, Ascendara asks where to download each game.

Make sure the drive has room for both the downloaded archive and the extracted game. The game page shows the download size and an estimated **Installation Size**.

## Download Methods

Open a game and choose a provider under **Download Options**. The available methods depend on the provider.

### Seamless Downloads

GoFile and PixelDrain downloads start directly in Ascendara with no browser and no CAPTCHA. Select the provider and click **Download Now**.

### Browser Extension

For other providers, the download page in your browser has a CAPTCHA or countdown. With the [Ascendara Download Handler](https://ascendara.app/extension) extension:

1. Open the download link in your browser and enable the extension.
2. Complete the CAPTCHA and start the download.
3. Allow your browser to open Ascendara. The extension sends the file link to Ascendara.
4. Review the game name and continue to download.

The first time you download a game, Ascendara offers a step-by-step guide. Read [Staying Safe](/docs/getting-started/protect-yourself) to avoid fake download buttons.

### Downloading From Any Website

The extension can also send downloads from websites and games that aren't in the Ascendara index. Choose **Any website** or **Capture next download** in the extension, and start a download in your browser. Ascendara opens **Download from your browser**, where you:

1. Enter the game's name and version, and whether online play or DLC is included.
2. Optionally search SteamGridDB for a cover, background, and logo.
3. Continue to the usual download and library flow.

Direct HTTP and HTTPS file links from any provider are supported. Links that require browser cookies or an active browser session may not work.

### Manual Link

Without the extension, you can paste a direct download link (DDL):

1. Copy the provider link into your browser.
2. Complete the CAPTCHA and start the download.
3. Stop the download once it starts.
4. Press **Ctrl+J** to open your browser's downloads, and copy the link of the file.
5. Paste it into Ascendara and click **Download Now**.

For Buzzheavier, Ascendara opens a popup window: double-click the download button in the popup to start the download.

### TorBox

With a TorBox API key, hoster downloads from 1Fichier and MegaDB go through TorBox and are converted into premium direct links. See [TorBox](/docs/features/torbox-integration).

### Torrents

Torrent downloads are off by default. To enable them, turn on **Torrent on Ascendara** in **Settings** and read the warning. Torrenting requires qBittorrent with its Web UI enabled (or TorBox), and using a VPN is strongly recommended because peers can see your IP address. See the [Torrent Handler](/docs/binary-tool/torrent-handler) for setup details.

## Following Your Downloads

Open **Downloads** to follow progress. Each download shows its speed, progress, and estimated time, and the page shows a speed graph and disk activity. Downloads go through separate stages:

1. **Downloading**: The file is transferred.
2. **Extracting**: The archive is extracted. This can take up to 30 minutes for large games.
3. **Verifying**: Installed files are checked against the expected sizes.

The game appears in your Library while it downloads, and is ready to play when the download card disappears. If you close Ascendara's window, downloads continue in the background.

### Download Controls

- **Pause** and **Resume**: Resumes where it left off when the server supports it. Otherwise the transfer restarts.
- **Kill Download**: Stops the download and asks whether to **Keep downloaded files** or **Delete downloaded files**.
- **Retry Download**: Restarts a failed download.
- **Retry Extraction**: Extracts the retained archive again without downloading it again.
- **View Live Log**: Shows the download manager log.
- **Verify Game Files**: Checks an installed game's files. Available for games downloaded with Ascendara 8.1.2 or later.

### Download Queue

When a download is already running, starting another one asks whether to add it to the queue or start it now. Queued downloads start automatically when the current one finishes.

Without Ascend, you can have 1 active download and 1 queued download, and simultaneous downloads aren't available. [Ascend](/docs/features/ascend) removes these limits.

### Remote Control

With [Ascendara Companion](/docs/features/companion), you can follow and control your downloads from your phone or another computer.

## When a Game Folder Already Exists

If a folder with the game's name already exists in your download directory, Ascendara asks what to do:

- **Rename Existing Folder**: Renames the old folder (adding `_OLD`) and downloads into a fresh one.
- **Delete Existing Folder**: Permanently deletes the old folder first.
- **Merge With New Download**: Downloads into the existing folder. This can corrupt game data if the contents don't match.

If the game is already in your library, Ascendara warns you before reinstalling it.

## Installers

Some downloads, such as torrents that contain a setup program instead of an archive, need a manual installation step. The download card shows **One Last Step**: run the installer as administrator, set its destination to the path shown, and select **I've Finished Installing** when it's done.

## Download Settings

These settings are in **Settings**, most of them in the **Download Manager** section. You can't change the download manager settings while a download is running.

| Setting | What it does |
| --- | --- |
| **Default Download Location** and **Additional Locations** | Where games are installed. |
| **Protect Directories from Defender** | Excludes your download directories from Windows Defender (Windows only). See [Protecting Directories](/docs/features/overview#protecting-directories-from-windows-defender). |
| **Single Stream Download** | Uses one connection per file. On by default; more stable for large files. |
| **Download Threads** | When Single Stream is off, how many connections to use. The downloader uses at most 16 connections per file. |
| **Download Speed Limit** | A maximum download speed. `0` means unlimited. |
| **Behavior After Download** | Do nothing, lock, sleep, or shut down the computer when a download finishes. |
| **Automatically Create Shortcuts** | Creates a desktop shortcut when a game finishes downloading (Windows). |
| **Desktop Notifications** | Shows a notification when downloads finish. |

## Common Download Errors

See [Common Issues](/docs/troubleshooting/common-issues#download-issues) for solutions to connection errors, expired links, missing files, extraction failures, and files removed by antivirus software.
