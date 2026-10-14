# Using Ascendara

This guide walks through first-time setup and the everyday flow of finding, downloading, and playing games. To install the app, see [Installing Ascendara](/docs/getting-started/installation).

## First-Time Setup
When you first launch Ascendara, the welcome setup guides you through:

1. **Language**: Choose one of the built-in languages, or see the full list of 105 languages.
2. **Terms**: Read and accept the Terms of Service and Privacy Policy.
3. **Free app notice**: Downloading, installing, and playing is free. [Ascend](/docs/features/ascend) is optional.
4. **Browser extension**: Install the [Ascendara Download Handler](https://ascendara.app/extension) (recommended).
5. **How you found Ascendara**: An optional question.
6. **Download location**: The folder where games are installed. You can add more folders later in **Settings**.
7. **Protect your directories** (Windows): Exclude your download folder from Windows Defender so false positives aren't deleted mid-download. Windows asks for administrator permission. This only works with Windows Defender.
8. **Theme**: Choose a light or dark theme.
9. **Import your games**: Optionally [import installed games](/docs/features/importing) from Steam, Epic Games, GOG Galaxy, Ubisoft Connect, or Battle.net.
10. **Updates**: Choose automatic or manual Ascendara updates.
11. **Compatibility tools** (Linux and macOS): Install Proton CachyOS and UMU-Launcher (Linux) or Wine (macOS).
12. **Game dependencies** (Windows): Install common runtime libraries:
    * .NET Framework
    * DirectX
    * OpenAL
    * Visual C++ Redistributable
    * XNA Framework

Ascendara downloads the official game index in the background during setup. If you're offline, you can continue and the games will appear once Ascendara can connect. After setup, an optional tour shows you around the app.

The game dependencies are runtime libraries that most games need. If you've played games on this PC before, you can skip this step. You can reinstall them at any time from **Settings** → **Application Components**.

## Finding Games
Use **Search** to browse the game index. You can filter by category, size, online support, and DLC, and sort by popularity, name, or recent updates. Press **Ctrl+K** (**Cmd+K** on macOS) anywhere to search games, settings, and commands.

The game list comes from [your game index](/docs/features/refreshing-index), which updates automatically in the background.

### Game Badges and Icons
* **ONLINE**: Online multiplayer is enabled through Online-Fix. Online-Fix games require Steam to be running.
* **ALL-DLCs**: All available DLC is included.
* **Verified Game**: The Ascendara community verified the game works with all of Ascendara's features.
* **Star rating**: The average rating from people who downloaded and played the game on Ascendara. Open the game to read their reviews.
* **Seamless download**: The game can be downloaded inside the app without a browser.

## Downloading a Game
Open a game to see its details, system requirements, and download options. Choose a provider, then start the download. See [Downloading Games](/docs/features/downloading) for every method in detail.

### Seamless Downloads
Supported providers (GoFile and PixelDrain) download directly in Ascendara with no browser and no CAPTCHA.

### Using the Download Handler Extension (Recommended)
1. Open the download link in your browser and enable the extension.
2. Complete the CAPTCHA and start the download.
3. The extension sends the file link to Ascendara. Review the game name, then continue to download.

### Manual Method
1. Copy and paste the link into your browser.
2. Complete the CAPTCHA and start the download.
3. Stop the download once it starts in your browser.
4. Press **Ctrl+J** to open your browser's downloads.
5. Copy the link the browser started downloading.
6. Paste the direct download link (DDL) into Ascendara and click **Download Now**.

Follow progress on the **Downloads** page. Downloading, extracting, and verifying are separate stages, so wait for the game to finish installing before you play.

## Your Library
All downloaded, custom, and imported games appear in your **Library**. Click a game to open its page, where you can:
* **Play** the game
* Manage executables, launch options, and desktop or Start Menu shortcuts
* Back up and restore saves
* Verify game files, open the game folder, or delete the game

You can also organize games into folders, mark Favorites, and use Play Later. See [Your Library](/docs/features/library).

### Adding Your Own Games
1. In the Library, select **Add to Library**.
2. Choose **Single game** to pick one executable, or **Scan folder** to add every game in a folder's subfolders.
3. Enter the game's details and save.

You can also drag and drop a game's `.exe` file onto the Library. Adding a game never modifies its files, and **Remove from Library** doesn't delete them.

## Updating Games
Ascendara shows an **Update** badge when a newer version of a game is available in the index. With [Ascend](/docs/features/ascend), updates can be handled automatically. Without Ascend, refresh your index and reinstall the game to get the latest version.

Updating reinstalls the game into the same folder. Most games store saves elsewhere (usually in AppData or Documents), but back up important saves first. See [Game Backups](/docs/features/game-backups).

## Reporting Issues
* **Game issues**: Click **Report** next to the game name on its download page.
* **App bugs**: Right-click anywhere in the app and select **Report Issue**.
* **Getting support**: Join the [Discord](https://ascendara.app/discord). If you're given a support code, enter it in **Settings** to upload your logs.

## Tips
* Use the Download Handler extension for easier downloads.
* Rate and review games you've played to help others.
* Keep your game dependencies installed and up to date.
* Press **Ctrl+1** through **Ctrl+6** to switch between Home, Search, Library, Downloads, Profile, and Settings.
