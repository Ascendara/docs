# Downloading Games

Ascendara downloads, extracts, verifies, and installs games for you. This page explains every way to start a download, how to follow and control downloads, and the download settings. For the technical details of the download pipeline, see the [Download Manager](/docs/binary-tool/downloader) binary documentation.

## Before You Start

Choose a download directory during setup or in **Settings** → **Download Manager**. You can add more directories under **Additional Locations**. When more than one directory is set, Ascendara asks where to download each game.

Make sure the drive has room for both the downloaded archive and the extracted game. The game page shows the download size and an estimated **Installation Size**.

## Download Folder Check

Ascendara checks a download folder before games go into it, so problems show up right away instead of halfway through a download. The check runs during the welcome setup, right after you choose a folder (and protect it, on Windows), and in **Settings** → **Download Manager** whenever you change the **Default Download Location** or add an **Additional Location**. It takes a few seconds.

The check has two parts:

1. **File access**: Ascendara creates a temporary folder, then writes, appends to, reads back, renames, and deletes a test file to make sure it can manage files there.
2. **Antivirus test**: Ascendara downloads an online-fix file that antivirus software often removes from game downloads. The file is saved to the folder just like a real download, watched for about five seconds to make sure it isn't removed or changed, and then moved and deleted to make sure it isn't locked. Ascendara then repeats the test with a second file, so a scanner that lets one file through doesn't hide a problem.

If everything passes, you can download games to that folder. In **Settings**, a passed check folds away on its own. If there's a problem, Ascendara explains what it found and you can **Try again**. Select **Details** to see the exact errors. During setup, you can also **Continue anyway** or **Skip check**, and the setup summary shows whether the check passed.

### The Test Files

Both test files are `OnlineFix64.dll`, taken unchanged from two real games available on Ascendara. Online-fix files like these let games connect for online play, and antivirus software often flags them as false positives and removes them, which breaks the game. Testing with the same kind of file a game download contains shows exactly what would happen to a real game in that folder.

Ascendara downloads them from its own server:

- `https://cdn.ascendara.app/setup/1/OnlineFix64.dll`
- `https://cdn.ascendara.app/setup/2/OnlineFix64.dll`

During the check, they're saved in a temporary hidden folder inside the download folder you chose, named `.ascendara-check-` followed by a random ID, with each file in its own subfolder. Ascendara never runs or loads the files: it only writes them, reads them back to check that they're still there and unchanged, and moves and deletes them. The temporary folder and everything in it are removed when the check finishes, whether it passes or not.

If your antivirus shows an alert about `OnlineFix64.dll` while the check runs, that's the test working as intended: the same alert would have removed files from a real game download.

### Folder Access Problems

**Ascendara can't use this folder** means the file access test failed, so downloads will likely fail there too. This usually happens with folders that need administrator permission (such as `C:\Program Files`), read-only or disconnected drives, and folders that a sync or backup tool locks. Choose a different folder, ideally on a local drive in a location you own, such as `C:\Games`.

### Antivirus Interference

**Your antivirus is interfering with downloads** means a test file was blocked while it downloaded, removed or changed after it downloaded, or locked so it couldn't be moved or deleted. Your antivirus is treating game files as threats, and it may delete important game files while they download, which stops games from working.

- **Windows Defender**: Select **Protect this folder** (setup) or **Protect folder** (Settings) to exclude your download folders from Defender. Windows asks for administrator permission, and the check runs again once the folder is protected. See [Protecting Directories from Windows Defender](/docs/features/overview#protecting-directories-from-windows-defender).
- **Other antivirus software**: Ascendara can't change its settings. Add your download folder as an exclusion in your antivirus, then select **Try again**.

See [Antivirus False Positives](/docs/getting-started/protect-yourself#antivirus-false-positives) for why game files get flagged.

### Check Could Not Finish

**The check couldn't finish** means the test files couldn't be downloaded, so the antivirus test didn't run. It doesn't mean anything is wrong with the folder. Check your internet connection, VPN, or firewall, then select **Try again**.

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
| **Default Download Location** and **Additional Locations** | Where games are installed. Each new location runs the [download folder check](#download-folder-check). |
| **Protect Directories from Defender** | Excludes your download directories from Windows Defender (Windows only). See [Protecting Directories](/docs/features/overview#protecting-directories-from-windows-defender). |
| **Single Stream Download** | Uses one connection per file. On by default; more stable for large files. |
| **Download Threads** | When Single Stream is off, how many connections to use. The downloader uses at most 16 connections per file. |
| **Download Speed Limit** | A maximum download speed. `0` means unlimited. |
| **Behavior After Download** | Do nothing, lock, sleep, or shut down the computer when a download finishes. |
| **Automatically Create Shortcuts** | Creates a desktop shortcut when a game finishes downloading (Windows). |
| **Desktop Notifications** | Shows a notification when downloads finish. |

## Common Download Errors

See [Common Issues](/docs/troubleshooting/common-issues#download-issues) for solutions to connection errors, expired links, missing files, extraction failures, and files removed by antivirus software.
