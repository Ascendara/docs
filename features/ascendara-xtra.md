# Game Information

Ascendara shows rich information about each game on its download page and on the game page in your Library: descriptions, screenshots, release dates, developers and publishers, genres, achievements, soundtracks, and more. This feature was previously called **Ascendara Xtra**.

> **Note:** No setup or API keys are required. Game information is loaded automatically.

## Where the Information Comes From

| Information | Source |
| --- | --- |
| Description, screenshots, release date, developers, publishers, genres, platforms | Steam, through the Ascendara API |
| Achievement names, descriptions, and icons | Steam, through the Ascendara API |
| Time to beat (Main Story, Main + Extras, Completionist) | [HowLongToBeat](https://howlongtobeat.com) |
| Cover, background, and logo artwork for imported and custom games | [SteamGridDB](https://www.steamgriddb.com), through the Ascendara API |
| Soundtracks | [KHInsider](https://downloads.khinsider.com) |
| Library value estimates | [CheapShark](https://www.cheapshark.com) |
| Star ratings and reviews | Players on Ascendara |
| Size, version, system requirements, download links | The [game index](/docs/features/refreshing-index) |

Ascendara matches games to Steam by name. When a match isn't exact, the page shows **Game information may be inaccurate**.

## On the Download Page

- **About This Game**, screenshots, and details from Steam
- **Minimum Requirements**, with **Compare Your PC** to check them against your hardware
- **Time to Beat** from HowLongToBeat
- **Community reviews**: Star ratings and written reviews from people who played the game on Ascendara
- **Installation Size**: An estimate of how much space the game takes after extraction
- Notices when a game needs VR, an emulator, or Steam running for Online-Fix

## On the Game Page

- **Overview**: Description, details, and your playtime
- **Achievements**: Achievements detected by the [Achievement Watcher](/docs/binary-tool/achievement-watcher), with unlock dates
- **Soundtrack**: Browse, play, and download the game's soundtrack. Tracks play in a mini player while you browse Ascendara.
- **Mods** and **Trainers**: Nexus Mods and FLiNG trainers for supported games (requires [Ascend](/docs/features/ascend))

## Fixing an Incorrect Match

If a game shows the wrong details or none at all:

1. Open the game from your Library.
2. Select **Edit Game Info**.
3. Search Steam for the game, or choose **Manual Entry** and enter the Steam App ID (the number in the game's Steam store URL, for example `1551360`).
4. Save. To remove a match, select **Clear Match**.

To change the game's artwork, select **Edit Assets** and search for new cover, logo, and banner images, or upload your own image.

## Rating Games

After you've played a game, Ascendara asks you to rate and review it. Your rating helps other players judge whether a game works well on Ascendara. Reviews are public, so only share details you're comfortable posting. Without an Ascend account, reviews are always anonymous. With Ascend, you can choose to share your review anonymously.

## Deprecated Providers

> **⚠️ Important Notice:** The following providers are no longer supported:
>
> - **GiantBomb**: GiantBomb discontinued their API service and no longer serves API requests. Old GiantBomb API keys are no longer needed.
> - **IGDB**: The IGDB integration was removed in favor of Steam data, which provides better coverage and requires no setup.
> - **OpenCritic**: Critic scores are no longer shown on game pages.

For additional help or questions, join the [Discord community](https://ascendara.app/discord).
