# Ascendara (asend-era)

## What is Ascendara?
Ascendara is a game discovery, download, and library app created by [tago](https://tago.works/). It browses a game index built from trusted sources, downloads and installs games for you (no manual extracting or setup), and keeps everything you play in one library. Ascendara is meant as a way to try games before you buy them, and it encourages you to support the developers of games you enjoy.

Ascendara became open source in May 2024 and is actively maintained by a solo developer. Everything from the website to the application source code is available at [ascendara.app](https://ascendara.app/) and on [GitHub](https://github.com/Ascendara/ascendara).

## Supported Platforms
- **Windows 10 and 11** (64-bit): the primary platform with full feature support.
- **Linux** (x64 AppImage): Windows games run through Proton, UMU-Launcher, or Wine. See [Linux and macOS](/docs/features/linux-and-macos).
- **macOS** (Apple Silicon and Intel): available from the GitHub releases. Windows games require Wine, and some features are Windows-only.

On Linux and macOS, Ascendara shows a compatibility notice on first launch. Windows is recommended for the best experience.

## Outsourced Links
Game files are never stored on Ascendara's servers. The game index contains links to third-party file hosts such as GoFile, Buzzheavier, PixelDrain, MegaDB, and others. Ascendara only organizes those links and handles the download and installation on your computer. Read the Digital Millennium Copyright Act (DMCA) notice [here](https://ascendara.app/dmca) to learn how to make a claim of copyright infringement.

## Core Principles

### Free to Use
Downloading, installing, and playing games is completely free and does not require an account. [Ascend](/docs/features/ascend) is an optional subscription that adds social, cloud, and automation features and funds development.

### Safety and Privacy
Ascendara does not require personal details or an account to download or play games. Your library, playtime, and profile level are stored on your computer unless you choose to sync them with Ascend.

### Public API
The game index is served by a public API at `api.ascendara.app` that is free for non-commercial use. See the [API Reference](/docs/api-reference/overview).

## Key Features

### Discover and Download
- **Game Index**: A local copy of the game list is kept up to date automatically so browsing is instant and works offline. See [Your Game Index](/docs/features/refreshing-index).
- **Seamless Downloads**: Supported providers download inside the app with no browser or CAPTCHA.
- **Browser Downloads**: The Ascendara Download Handler extension sends downloads from your browser to Ascendara, including games that aren't in the index.
- **Download Manager**: Downloads, extracts, verifies, and installs games, with a download queue, speed limits, and resumable transfers. See [Downloading Games](/docs/features/downloading).
- **TorBox and Torrents**: Optional debrid and qBittorrent support. See [TorBox](/docs/features/torbox-integration).
- **External Sources**: Load games from a community source bucket instead of the official index. See [External Sources](/docs/features/external-sources).

### Library and Play
- **One Library**: Downloaded games, custom games, and games [imported](/docs/features/importing) from Steam, Epic Games, GOG Galaxy, Ubisoft Connect, and Battle.net.
- **Organization**: Folders, Favorites, Play Later, History, and Smart Collections. See [Your Library](/docs/features/library).
- **Game Details**: Steam details and screenshots, time to beat, soundtracks, achievements, and community reviews. See [Game Information](/docs/features/ascendara-xtra).
- **Save Backups**: Back up and restore saves with [Ludusavi](/docs/features/game-backups).
- **Big Picture Mode**: A controller-first interface for the couch. See [Big Picture Mode](/docs/features/big-picture).
- **Ascendara Retro**: A separate library for classic console games launched through your own emulators. See [Ascendara Retro](/docs/features/retro).

### Personalization and Tools
- **Themes**: Built-in light and dark themes, custom colors, and community themes.
- **105 Languages**: 12 built-in languages plus 93 more translated on your computer.
- **Discord Rich Presence**: Show what you're playing.
- **System Center**: Health checks, storage overview, settings recovery points, and version rollback. See [System Center](/docs/features/system-center).

## Getting Started

### Quick Setup
1. [Install Ascendara](/docs/getting-started/installation).
2. Follow the welcome setup: choose a download folder, theme, and your game index.
3. Install the game dependencies when prompted (Windows).
4. Start browsing games.

### For the Best Experience
- Install the [browser extension](https://ascendara.app/extension).
- On Windows, enable **Protect Directories from Defender** so antivirus false positives don't remove game files.
- Read [Staying Safe](/docs/getting-started/protect-yourself) and the [usage guide](/docs/getting-started/usage).
- Join the Ascendara [Discord](https://ascendara.app/discord) community.

## Community and Support
- [Discord community](https://ascendara.app/discord) for help and discussion
- [Feedback](https://ascendara.app/feedback) and [roadmap](https://ascendara.app/roadmap)
- [Changelog](https://ascendara.app/changelog)
- [GitHub issues](https://github.com/Ascendara/ascendara/issues) for bug reports
