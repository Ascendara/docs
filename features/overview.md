# Welcome to Ascendara

Ascendara is your all-in-one gaming companion for discovering, downloading, and managing games. This page gives a tour of its features and links to the detailed guide for each one.

## Game Discovery Made Simple

Ascendara keeps a local copy of the game index on your computer and updates it automatically, so searching and browsing are instant and work offline. Filter by category, size, online support, and DLC, or sort by popularity, name, and recent updates. Each game page shows its system requirements, Steam details and screenshots, time to beat, and community reviews.

- [Your Game Index](/docs/features/refreshing-index)
- [External Sources](/docs/features/external-sources)
- [Game Information](/docs/features/ascendara-xtra)

## Advanced Download Management

Ascendara's download manager handles the whole process: transfer, extraction, verification, and installation. Interrupted downloads can resume where supported, archive problems are retried automatically, and you can queue downloads, limit speed, and choose what your computer does when a download finishes.

- [Downloading Games](/docs/features/downloading)
- [TorBox Debrid Service](/docs/features/torbox-integration)
- [Download Manager (technical)](/docs/binary-tool/downloader)

## Download Games in 2 Clicks

Games hosted on supported providers can use **seamless downloads**: Ascendara resolves the link and starts the download itself, with no browser and no CAPTCHA. Seamless downloads are currently supported for **GoFile** and **PixelDrain**. Look for the seamless download icon on a game to see whether it's supported.

If you use [TorBox](/docs/features/torbox-integration), you can also enable **Prioritize Torbox over Seamless** to download through TorBox whenever both options are available.

## Protecting Directories from Windows Defender

Windows Defender often flags game files, especially online-fix and crack files, as false positives and removes them during or after installation. Ascendara can exclude your download directories from Defender scans so this doesn't happen.

To enable it, open **Settings**, go to the **Download Manager** section, and turn on **Protect Directories from Defender**. You can also enable it during the welcome setup. Windows will ask for administrator permission.

When enabled, Ascendara runs PowerShell with administrator privileges and adds your default download directory and every additional directory to Defender's exclusion list using `Add-MpPreference -ExclusionPath`. Turning the setting off removes those exclusions again with `Remove-MpPreference`.

This feature only works with Windows Defender. If you use a different antivirus, Ascendara can't change its settings: add your download folders as exclusions in that antivirus yourself. If files go missing after extraction, the Downloads page offers **Enable protection and retry** to protect the folder and restore the missing files from the retained archive.

## Your Library

All of your games live in one library: games downloaded through Ascendara, custom games you add yourself, and games [imported](/docs/features/importing) from Steam, Epic Games, GOG Galaxy, Ubisoft Connect, and Battle.net. Connect your Steam, Epic Games, or GOG account to also see games you own but haven't installed. Organize them with folders, Favorites, Play Later, and Smart Collections, and track playtime and achievements.

- [Your Library](/docs/features/library)
- [Importing Games](/docs/features/importing)
- [Game Backups](/docs/features/game-backups)

## Smooth Gaming Experience

Click **Play** and Ascendara handles the rest through its [Game Handler](/docs/binary-tool/game-handler): it launches the correct executable with your launch options, tracks playtime, updates Discord Rich Presence, and can back up your saves when the game closes. Some older games may need extra configuration or may not work on modern systems.

On Linux, Windows games run through Proton, UMU-Launcher, or Wine, and on macOS through Wine. See [Linux and macOS](/docs/features/linux-and-macos).

## Big Picture Mode

A controller-first interface for the couch. Ascendara offers to switch to Big Picture when it detects a controller. See [Big Picture Mode](/docs/features/big-picture).

## Ascendara Retro

A separate library for classic console games launched through emulators you already have. See [Ascendara Retro](/docs/features/retro).

## Ascendara Timemachine

The Ascendara Timemachine lets you download previous versions of a game. Enable it in **Settings** under **Ascendara Timemachine**. Once enabled, the game's download page lets you choose a previous version, with the date it was available, and switch the download to that version.

Download links for older versions often stop working over time, so success rates vary, especially for older games. The Timemachine is useful for preserving specific versions and for compatibility with mods or saves made on an older version.

## Modern Interface

Ascendara has light and dark themes, a theme editor for custom colors, and community themes you can browse and apply. Press **Ctrl+K** (**Cmd+K** on macOS) to open the command palette and search games, settings, and commands from anywhere.

## Community Trust & Safety

Players rate and review the games they've played on Ascendara, and games the community has confirmed to work with all of Ascendara's features show a **Verified Game** badge. You can report broken links or incorrect details from any game's page. See [Staying Safe](/docs/getting-started/protect-yourself).

## Username Customization

Many games that use the Goldberg Steam emulator show a player name in-game. Ascendara lets you set that name from your **Profile**: open the name dialog, enter a display name, and turn on **Use this name for Goldberg cracks**. You can also set the **Goldberg Settings Directory** if the emulator's settings are stored somewhere other than the default.

Some games don't use the Goldberg emulator, and in those cases changing your name won't have any effect. Those games have their own name settings or account requirements. There is no reliable way to detect which emulator a game uses, because the files can be similar across different cracks, so Ascendara can't tell you in advance whether the name will apply to a specific game.

## Ascendara Workshop Downloader

The Workshop Downloader downloads items from the Steam Workshop without a Steam account. Enable it in **Settings** with **Show in Navigation Bar** under **Workshop Downloader**, then open it from the navigation bar and paste workshop item links. Ascendara saves the downloaded items to a folder for you to manage yourself.

The Workshop Downloader uses SteamCMD. If it isn't installed, Ascendara installs the SteamCMD sidecar for you the first time. Some items can't be downloaded anonymously because they're private or restricted, or because the game requires a Steam account that owns it. The Workshop Downloader is not available on macOS.

## Ascend

Ascend is Ascendara's optional subscription. It adds friends and chat, cloud sync and save backups, automatic game updates, unlimited download queues, Ascendara Companion, mods and trainers, and more. See [Ascend](/docs/features/ascend) and [Ascendara Companion](/docs/features/companion).

## Continuous Evolution

As an open-source project, Ascendara keeps evolving with new features and improvements driven by community feedback. Check the [changelog](https://ascendara.app/changelog) and [roadmap](https://ascendara.app/roadmap) to see what's new and what's next.
