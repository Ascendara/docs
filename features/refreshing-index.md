# Your Game Index

Ascendara keeps a copy of the game index on your computer. Searching, filtering, and browsing read from this local copy, so games appear instantly and you can browse even when you're offline or Ascendara's servers are unavailable.

You don't need to build or refresh the index yourself. Ascendara downloads the official index during setup and keeps it up to date in the background.

> **What changed?** Earlier versions of Ascendara asked you to build your index by scraping SteamRIP on your own computer with a Cloudflare cookie, or to download a community-shared index. Both flows have been removed. The Ascendara API now refreshes the official index on a schedule, and the app syncs it automatically. The scraping tool still exists for developers; see [Local Refresh](/docs/binary-tool/local-refresh).

## Opening Your Game Index

Open **Settings** and select **Game Index** in the list on the left. In [Big Picture Mode](/docs/features/big-picture), the same options are available from the index manager.

The **Ascendara Index** card shows how many games your index contains, when it was last refreshed, and the date of the index itself. The **Settings** card next to it holds the index options described [below](#settings).

## Choosing an Index

You can use one of two sources. The Ascendara Index is used unless you turn on External Sources.

### Ascendara Index (Recommended)

The official index, curated and shared by Ascendara. It includes:

- Game names, sizes, versions, and download links
- Cover images
- Categories, popularity, and ratings
- Minimum system requirements

This is the default, and it supports every Ascendara feature.

### External Sources

External Sources Mode loads games from a community source bucket or from your own imported JSON lists instead of the official index. External sources don't include cover images, categories, or popularity data.

To use one, select **Manage sources** at the bottom of the Settings card in **Settings → Game Index**. This opens the **External Sources** page. See [External Sources](/docs/features/external-sources).

While an external source is active, the Game Index section shows **An external source is active** instead of the Ascendara Index card. Select **Use Ascendara index** there to switch back.

## How the Index Stays Up to Date

When **Keep Index Up to Date** is on (the default), Ascendara:

1. Checks for a newer index a few seconds after launch, so an outdated index is replaced before you start browsing.
2. Checks again every 6 hours while Ascendara is running.
3. Only downloads the game list when it has changed.

If a check fails, Ascendara retries up to 5 times, one minute apart. When the index changes, the game list reloads automatically.

To update right away, select **Refresh now** on the Ascendara Index card in **Settings → Game Index**. If you don't have an index yet, the button says **Get the index**.

Automatic syncing is paused while External Sources Mode is on.

### Cover Images

Covers are not downloaded all at once. After each sync, Ascendara downloads the covers for the most popular and most recently updated games in the background, and every other cover is downloaded the first time you see it. Covers are then kept in your index folder, and covers for games that leave the index are removed.

## Helping Refresh the Index

Sometimes the game source blocks Ascendara's servers from reading it. If that happens and the shared index hasn't been updated for more than 12 hours, **Settings → Game Index** shows **The game index can't update itself right now** with a **Help refresh** button.

When you select **Help refresh**, Ascendara fetches the latest game pages using your own connection and sends them to the Ascendara API, which builds the updated index. A small window may open and ask you to confirm you're human. Once it finishes, everyone using Ascendara receives the update, and your index syncs immediately.

Helping is optional. Your index continues to work while the shared index is out of date.

## Settings

The **Settings** card in **Settings → Game Index** contains:

### Keep Index Up to Date

Turns automatic background syncing on or off. See [How the Index Stays Up to Date](#how-the-index-stays-up-to-date). This option is hidden while an external source is active.

### Storage Location

Where the index is stored. The default location is:

| Platform | Default path |
| --- | --- |
| Windows | `%APPDATA%\ascendara\localindex` |
| macOS | `~/Library/Application Support/ascendara/localindex` |
| Linux | `~/.config/ascendara/localindex` |

Select the pencil icon to choose another folder. Ascendara doesn't move the existing files; the index is downloaded into the new folder on the next sync.

### Blacklist

**Blacklisted Games** hides specific games from your index. Games are identified by their 6-character Game ID. Enter a Game ID and press **Enter** or select **+**, and click **X** next to an ID to remove it. Blacklisted games are removed from the game list each time it syncs.

Ascendara includes three blacklisted IDs by default.

## Index Files

Your index folder contains:

| File | Purpose |
| --- | --- |
| `ascendara_games.json` | The game list. See the [game list format](/docs/api-reference/games-list). |
| `imgs/` | Cached cover images, named by image ID. |
| `sync.json` | The version (ETag) of the last downloaded game list, used to skip unchanged downloads. |
| `progress.json` | The status and time of the last successful update. |

Don't edit these files while Ascendara is running.

## Before Reporting a Broken Download

Refresh your index before reporting missing files or a corrupted download, then check the download again. The problem may already be fixed in the latest index. The report dialog on each game page reminds you of this.

## Troubleshooting

### No Games Appear After Setup

Ascendara couldn't download the game list, most likely because you were offline. Games appear automatically once Ascendara can connect. You can also open **Settings → Game Index** and select **Refresh now**.

### The Index Doesn't Update

- Make sure **Keep Index Up to Date** is on and External Sources Mode is off.
- Check the server status indicator in the navigation bar.
- Select **Refresh now**.

### Covers Are Missing

Covers download the first time you view a game, so they may take a moment to appear on a slow connection. Some games don't have a cover in the index.

### Help Refresh Fails

Make sure the verification window wasn't closed before it finished, then try again. The window times out after 3 minutes.
