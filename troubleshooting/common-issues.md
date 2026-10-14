# Common Issues and Solutions

Start with the [System Center](/docs/features/system-center) health check (**Ctrl+K** → **Run health check**). It checks your download folder, game index, Ascendara services, and dependencies, and offers fixes for common problems.

## Startup Issues

### "Cannot run in administrator mode"
Ascendara doesn't support running as administrator on Windows because it causes path problems. Close Ascendara and open it normally, without **Run as administrator**.

### "Achievement Service Not Running"
The standalone Achievement Watcher app conflicts with Ascendara's built-in achievement tracking because they use the same system. Uninstall Achievement Watcher, then restart Ascendara.

### Ascendara Shows a Blank or Broken Window
Open the command palette (**Ctrl+K**) and select **Reload interface**. If the problem continues, open **Open Recovery tools** and try **Safe UI Mode** or **Clear browser data**. Packaged builds serve the interface locally on port `46859`, so make sure no other application is using that port.

### "You're running a broken version"
This version has known critical issues. Update Ascendara to the latest version.

## Download Issues

### Slow Downloads
- Check your internet connection and try a different network.
- Check that **Download Speed Limit** in **Settings** is `0` (unlimited) or high enough.
- Turn off **Single Stream Download** to use more connections per file.
- Use a [TorBox](/docs/features/torbox-integration) account for premium hoster speeds.

### "The download connection was interrupted"
Your network or internet provider may be blocking the provider, or the connection is unstable. Try enabling **Single Stream Download** in **Settings** for a more stable download, try a VPN, or check your firewall and antivirus settings.

### "Provider Site Couldn't Load"
The download page is blocked or unreachable on your network, usually because your internet provider blocks the provider's website. Use a VPN and try again.

### Content Type Error
The download link from the provider has most likely expired. Some providers do this to prevent download abuse. Cancel the download and start it again.

### No Files Error
The downloader couldn't find any files to download, which usually means the file was removed from the provider. Try a different provider if one is available.

### "This game was removed from the download provider"
The file is no longer available on that provider. Refresh [your game index](/docs/features/refreshing-index) and try again, or choose a different provider.

### Gofile Rate Limit or Account Creation Failed
Gofile has rate limited your connection, or Ascendara couldn't create the temporary Gofile account it needs. Enable a VPN or wait a few minutes, then try again.

### "No space left on device"
Free up disk space on the download drive. You need room for both the downloaded archive and the extracted game.

### "Retry data not available"
Ascendara can't resume or retry this download because its data is missing. Delete the download and start it again from the game's page.

### Torrent Downloads Don't Start
Make sure qBittorrent is running with its Web UI enabled, and that the host, port, username, and password in **Settings** match qBittorrent. If the credentials are correct but Ascendara still can't connect:

1. Open qBittorrent and go to **Tools** → **Options** → **Web UI**.
2. Under **Authentication**, check **Bypass authentication for clients on localhost**.
3. Check **Bypass authentication for clients in whitelisted IP subnets** and add `127.0.0.1/32`.
4. Click **Apply**, then fully close and restart both qBittorrent and Ascendara.

If you use a VPN, proxy, or a tool like ExitLag, make sure it isn't rerouting localhost traffic.

## Extraction Difficulties
When you see "Cannot open file as archive" or **Archive extraction failed**, the download may be incomplete or corrupted. Use **Retry Extraction** on the Downloads page to extract the retained archive again without downloading it. If it fails again, the archive itself is damaged: delete the download and try a different provider.

Ascendara automatically retries incomplete extraction once and can re-download a damaged archive once. Make sure you have enough free space and write permissions in the download directory.

## Verification Issues
After a game is downloaded and extracted, Ascendara verifies the installed files. If verification fails, your antivirus has most likely removed files it flagged as false positives, which is common for online-fix and crack files.

- If the Downloads page shows **Files disappeared after extraction**, select **Enable protection and retry** (Windows Defender) or **Retry missing files**. Ascendara keeps the source archive while it waits for your choice, so the missing files can be restored.
- On Windows, turn on **Protect Directories from Defender** in **Settings** to prevent this in the future. If you use a different antivirus, add your download folders as exclusions in that antivirus.
- If you see **Windows detected a false positive and prevented the download from completing**, enable **Protect Directories** and download again.

If files are removed after a game was already installed and verified, the original archive is no longer available, so you need to download the game again. You can check an installed game at any time with **Verify Game Files** on its page. File verification is available for games downloaded with Ascendara 8.1.2 or later.

## Game Launch Failures
If a game fails to start, check that your system meets the minimum requirements and that all game files are present. For black screens, update your graphics drivers and make sure the game dependencies are installed (**Settings** → **Application Components** → **Reinstall Dependencies**). If the game crashes on startup, try running it in compatibility mode and close conflicting applications.

### "Cannot run games in developer mode"
Games can't be launched when Ascendara is run from source. Use a packaged build.

### Executable Not Found Launch Error
This error occurs when Ascendara can't find the game's executable. Game executables aren't always named after the game, and many games keep them in subfolders such as `bin`, `x64`, `x86`, or `Binaries`.

To fix it, open the game from your Library, select **Change or Add Executable**, and choose the correct file. Ascendara remembers it for future launches. A game can have several executables; the **primary** one runs when you click **Play**.

### "Game Closed Unexpectedly"
The game closed right after launching. Open the game folder and run the executable directly to check whether the game works outside of Ascendara. If it doesn't, the problem is with the game files or your system rather than Ascendara.

### "Steam is not running"
Online-Fix games require Steam to be running and signed in to an account. Start Steam, then launch the game again.

### Games That Behave Differently When Launched by Ascendara
Ascendara launches games through its Game Handler, which some games may not expect:

- **Anti-cheat and DRM**: Games with anti-cheat may block launches from another program, and strict DRM may require its own launcher.
- **Permissions**: Some games need administrator privileges.
- **Launchers and bootstrappers**: Some games start a small launcher that starts the real game. Ascendara keeps tracking playtime while any process from the game's folder is running.

If a game only works when launched directly, create a shortcut to the game's executable instead, or set the correct executable as the primary one.

## Troubleshooting Steps
1. Run the System Center health check.
2. Verify your internet connection and that the download completed.
3. Make sure there's enough disk space for the game.
4. Update your graphics drivers and reinstall the game dependencies.
5. Check that your system meets the game's requirements.
6. Use **Verify Game Files** to confirm the game's files are intact.
7. Ask for help in the [Discord community](https://ascendara.app/discord). If you're given a support code, upload your logs from **Settings**.

## Performance Issues
- **Lag or stuttering**: Close background applications and update your drivers.
- **High CPU usage during downloads**: Lower the number of download threads or turn on **Single Stream Download**.
- **Slow interface**: Turn off **Smooth Transitions** in **Settings**, or turn on **Safe UI Mode** in the System Center.

## Log Files

| Log | Windows | macOS | Linux |
| --- | --- | --- | --- |
| App (`debug.log`) | `%APPDATA%\Ascendara by tagoWorks\` | `~/Library/Application Support/Ascendara by tagoWorks/` | `~/.config/Ascendara by tagoWorks/` |
| Downloads (`downloadmanager.log`) | `%APPDATA%\Ascendara by tagoWorks\` | `~/.config/Ascendara by tagoWorks/` | `~/.config/Ascendara by tagoWorks/` |
| Game launches (`gamehandler.log`) | `%APPDATA%\Ascendara by tagoWorks\` | `~/Library/Application Support/ascendara/` | `~/.ascendara/` |

You can also open the download log from the Downloads page with **View Live Log**. Remove private download links before sharing logs.
