# External Sources and Game Index

Ascendara keeps a game index on your computer for fast browsing and offline access. By default it uses the official Ascendara index. External Sources Mode lets you load games from a community source bucket, imported JSON lists, or your own self-hosted library instead.

For how the official index works and stays up to date, see [Your Game Index](/docs/features/refreshing-index).

## Overview

The game index is a local list containing:

- Game names, sizes, and versions
- Download links and source references
- Cover images
- Categories, ratings, and popularity data when available

---

## Choosing a Game Source

Ascendara supports two source modes. Each offers a different balance of features and flexibility. You can switch between them at any time on the **External Sources** page: open **Settings → Game Index** and select **Manage sources**, then turn **Use external sources** on or off. While an external source is active, **Settings → Game Index** also offers **Use Ascendara index** to switch back in one click.

### Official Ascendara Index (Recommended)

The official index provides the full Ascendara experience and is the default option.

**Features**

- Full metadata support
- Categories, ratings, and popularity data
- Cover images
- Automatic background updates
- Full Ascendara feature compatibility

**Best for**
Users who want the fastest experience and full feature support.

### External Sources Mode

External Sources Mode loads games from an external source instead of the official Ascendara index. It is marked **Experimental** in the app.

This mode is designed for flexibility and custom sourcing. You can point Ascendara at a supported source bucket and pick one of its sources, import your own JSON lists, or connect a private server or folder using Self-hosted sources.

**Features**

- Supports external source bucket URLs
- Lets you choose your own source, and switch between sources you've used
- Supports your own imported JSON lists (Custom Lists)
- Flexible for custom or community-maintained sources

**Community bucket and Custom List limitations**

- No cover images, categories, popularity data, or ratings from the source
- Browsing by category is unavailable
- Sorting by popularity is unavailable
- Automatic background index updates are paused; sync the source manually instead

**Best for**
Users who want to use a different catalog.

---

## Source Buckets

A source bucket is a URL that hosts a collection of sources. Ascendara asks the Ascendara API to read the bucket, so only bucket hosts that the API recognizes are accepted. If a URL isn't recognized, Ascendara shows **This URL is not a recognized source bucket.**

### Setting Up a Source

1. Open **Settings → Game Index** and select **Manage sources**.
2. On the **External Sources** page, turn on **Use external sources**. The page reminds you that external sources have reduced metadata.
3. Paste a URL into **Source bucket URL**, or select **Use recommended**, then click **Set**.
4. Select **Browse sources**, search the list, and pick a source.
5. Ascendara loads the source and builds your game list from it.

Use **Sync Now** to reload the selected source and **Change** to pick a different one. Sources you've used before appear under **Your sources** so you can switch back without browsing the bucket again.

### Source Badges

- **Trusted**: Widely used by the community and known to be reliable and safe.
- **Rating**: The source's community rating, when the bucket provides one.
- **Torrent only**: The source only publishes magnet links. See [Torrent-Only Sources](#torrent-only-sources).

### When a Source Can't Be Fetched

Some source links are protected by a browser challenge that Ascendara can't solve on its own. In that case Ascendara shows **Couldn't fetch source automatically** and opens the link in your browser. Once the page loads the JSON, copy all of the text, paste it into the dialog, and Ascendara will use it.

### Torrent-Only Sources

Some sources only publish magnet links. To select one, you need to enable torrenting in **Settings**, which requires qBittorrent. Torrenting exposes your IP address to peers, so using a VPN is strongly recommended. While a torrent-only source is selected, torrenting can't be turned off; change the external source first.

---

## Self-Hosted Sources

Connect a private game server, NAS, or folder from **External Sources → Add Self-Hosted Source**. You do not need an Ascendara account or a recognized community source bucket. Private sources appear in the existing game catalog; selecting one replaces the catalog you are currently browsing, without changing installed games.

### Connect Your Library

1. Open **Settings → Game Index → Manage sources**.
2. Under **Self-hosted sources**, select **Add Self-Hosted Source**.
3. Choose where your games are stored:
   - **Web server**: enter the full HTTP or HTTPS URL of a compatible private `index.json`, such as `http://192.168.1.100:8080/index.json`. An ordinary directory-listing page is not an index.
   - **Network share**: on Windows, enter a UNC path such as `\\UNRAID\Games`. Windows uses your OS-managed share credentials, so a drive letter is not required. On macOS and Linux, connect the share through your operating system first, then select its mounted folder.
   - **Local folder**: select a folder on your device. Ascendara scans its subfolders too.
4. Give the library a name. Optionally enable **Find artwork and game details automatically**.
5. Select **Connect library**, then **Browse games** on the saved source.

Use **Learn more** in the source panel or connection dialog to return to this guide.

### Discovery and Advanced Settings

Folder sources recognize `.exe`, `.iso`, `.zip`, `.7z`, and `.rar` packages. Names are cleaned for display: for example, `Cyberpunk.2077.Setup.exe` becomes `Cyberpunk 2077`. Discovery does not prove that an executable is a game or installer, and scanning never executes it.

Open **Advanced settings** to configure:

- **Bearer token**: optional authentication for HTTP sources, saved using OS-protected credential storage. Leave the field blank when editing to keep an existing token, or choose **Remove saved token**. Tokens cannot be saved when secure OS storage is unavailable.
- **Skip folders**: one relative directory path per line, such as `Backups` or `Old versions`.
- **Skip files**: one relative file path per line, such as `Tools/setup.exe`.

Use forward slashes for nested exclusion paths. These are exact paths, not wildcard patterns. Folder exclusions also skip their descendants. Symlinks are not followed during discovery.

HTTP is unencrypted. Prefer HTTPS outside a trusted network, especially when using a token. Redirects are rejected; enter the final index URL directly.

### Artwork and Game Details

Automatic matching is optional and sends cleaned game titles directly to Steam. No metadata API key is required. Each refresh checks up to 25 previously unmatched entries. Only an unambiguous exact title match is accepted automatically; uncertain matches require your selection. Results and downloaded artwork are cached on the device.

Open a game's details and select **Match Game / Edit details** to search Steam and compare candidates by artwork, title, and title similarity. You can also save a custom title and description. **Upload artwork** accepts PNG, JPEG, or WebP images up to 2 MB.

Steam can supply a description, release date, genres, developer, publisher, and screenshots. Your source can also provide metadata and artwork. If a provider is unavailable, browsing continues with cached or local information; missing artwork uses a placeholder.

### Downloads and Installation

Choose a library destination on the game details page. **Download / Queue** queues the package when another download is active; **Start concurrently** starts it immediately. Transfers appear in Ascendara's normal **Downloads** page.

- HTTP downloads verify the indexed file size and the SHA-256 checksum when supplied. Partial downloads can resume when a checksum is present and the server honors range requests. If ranges are unavailable, the transfer restarts from the beginning.
- Local and network-share packages are copied with progress and cancellation. Interrupted copies restart from the beginning.
- Downloaded packages remain in a review state. They are not marked ready to play just because the transfer finished.
- ZIP, 7z, and RAR packages offer **Extract archive**, using Ascendara's existing extraction tools. Available extraction capabilities depend on your platform and installed helpers.
- EXE installers must be reviewed and run manually. Automatic ISO mounting is not supported; use your operating system's mounting tools where available. The app never silently runs private installers.
- After extraction or installation, use **Select installed game executable** to import a game inside its library folder. For games installed elsewhere, use the existing Library add-game workflow.

### Refresh, Switch, or Remove

Use the refresh button beside a source after changing its files or index. A failed refresh shows an offline status and an error; previously indexed games remain available to browse. Restart the server or reconnect the share, then refresh again.

**Browse games** switches to a source. The edit button changes its settings, and the export button saves its portable index. Removing a source removes its configuration, not downloaded games. To return to the public catalog, select **Use Ascendara index** in Game Index settings.

### Private Index Format (Version 1)

HTTP sources use a separate format from community source buckets and imported Custom Lists:

```json
{
  "version": 1,
  "sourceId": "my-private-library",
  "name": "My game library",
  "games": [
    {
      "id": "sample-game",
      "title": "Sample Game",
      "path": "games/Sample Game.zip",
      "size": 1024,
      "type": "zip",
      "identifiers": {},
      "artwork": {
        "cover": "artwork/sample-cover.jpg",
        "hero": "artwork/sample-banner.jpg"
      },
      "metadata": {
        "description": "A description of your game.",
        "releaseDate": "2026-01-01",
        "genres": ["Adventure"],
        "developer": "Example Studio",
        "publisher": "Example Publisher"
      }
    }
  ]
}
```

Replace the example size with the actual size in bytes. Required fields are `version`, `sourceId`, `games`, and each game's unique `id`, relative `path`, and integer `size`. Titles fall back to cleaned filenames. `type`, when supplied, must match the file extension. IDs use letters, numbers, spaces, underscores, periods, or hyphens, starting with a letter or number, and are limited to 100 characters.

Optional `sha256` contains the file's 64-character hexadecimal SHA-256 checksum. Optional `identifiers.steam` contains a numeric Steam app ID as a string. Artwork and metadata are optional. Indexes are limited to 8 MB and 10,000 entries; files are limited to 16 TiB each.

Package and artwork paths use forward slashes and are relative to the directory containing `index.json`. Absolute paths, external artwork URLs, traversal segments such as `../`, and URL query strings in references are rejected. Keep files beneath the source root. A source's downloaded artwork is limited to 2 MB per image.

Use the source's export button to generate an index from a scanned folder. Serve that index beside its referenced files to connect the same library from other devices. Local overrides, uploaded artwork, credentials, and caches remain device-local; export includes supported title and metadata changes but does not bundle uploaded artwork or credentials.

### Local Development Test Server

From the Ascendara application repository, run:

```powershell
python tests/fixtures/self-hosted/server.py
```

Add a **Web server** source with `http://localhost:8080/index.json`. The server generates safe fixtures under `tests/fixtures/self-hosted/data`: inert EXE and ISO files, a ZIP containing a README, and sample artwork. The EXE and ISO are deliberately not executable or mountable games.

The server binds to localhost on port 8080 by default and supports range downloads. To test another device on your LAN, explicitly use `--lan` and connect to the server computer's LAN address. Optional `--token YOUR_TEST_TOKEN` enables Bearer authentication; `--no-range` tests restart behavior without range support. Only use a disposable test token.

To test a folder source, run the server once (or use `--generate-only`), then select its generated `data` folder. Stop the server and refresh the HTTP source to test the offline state; restart and refresh to reconnect.

---


## Custom Lists

Custom Lists let you import your own game JSON and use it as your source.

1. On the **External Sources** page, with **Use external sources** turned on, select **Import New List** under **Custom Lists**.
2. Paste the JSON, or select **Paste from clipboard**, and click **Process JSON**.
3. Review the detected structure, keys, and number of items, then give the list a name.
4. Import the list. It becomes the active list.

Select a list to switch to it. You can also rename or delete lists. Lists are stored as JSON files in `Documents/Ascendara/CustomLists`.

---

## Supported Formats

Community sources and Custom Lists accept two JSON structures. Self-hosted sources use the separate versioned format described above.

### 1. Games Array

The same structure as the official Ascendara index:

```json
{
  "games": [
    {
      "game": "Sample Game",
      "gameID": "AbCdEf",
      "imgID": "sample123",
      "dirlink": "https://example.com/game-download/",
      "download_links": {
        "gofile": ["https://gofile.io/d/example"]
      },
      "version": "1.0.0",
      "size": "15.0 GB",
      "latest_update": "2026-04-21",
      "category": ["Action", "Adventure"],
      "online": false,
      "dlc": false
    }
  ]
}
```

When this structure is used as an external source, Ascendara reads each game's name (`game`, `title`, or `name`), its links (`download_links`, `dirlink`, `url`, `uri`, `uris`, or `magnet`), its size (`size` or `fileSize`), and its date (`latest_update`, `uploadDate`, or `date`). Other fields such as categories, ratings, and images are not used by the community source and Custom List import flow. See the [game list format](/docs/api-reference/games-list) for all fields of the official index.

### 2. Downloads Array

A simplified collection format focused on titles, links, sizes, and upload dates:

```json
{
  "name": "SampleCollection",
  "downloads": [
    {
      "title": "Sample Game Free Download (v1.0.0)",
      "fileSize": "500 MB",
      "uris": [
        "https://example.com/download1",
        "magnet:?xt=urn:btih:example"
      ],
      "uploadDate": "2025-03-05T16:41:20.000Z"
    }
  ]
}
```

Ascendara reads the game name and version from the title, so titles like `Game Name (v1.0.0)` work best.

---

## Images in External Sources Mode

Community source buckets and Custom Lists do not supply artwork through their import format. Ascendara tries to find artwork for each game from SteamGridDB by matching the title. Results depend on how closely the title matches, so some games may have incorrect or missing artwork.

---

## Troubleshooting

### The source bucket isn't accepted
- Make sure the URL is correct and complete.
- Only bucket hosts recognized by the Ascendara API are supported. Try **Use recommended**.

### A source doesn't load
- Select **Sync now** to try again.
- If Ascendara asks you to paste the JSON manually, open the link in your browser and copy the entire page.
- Check that the source uses one of the supported formats.

### Games are missing metadata
- Community sources and Custom Lists have reduced metadata. For a self-hosted source, open a game and select **Match Game / Edit details**. For categories, ratings, popularity sorting, and covers, switch back to the official Ascendara index.

### I can't select a source
- The source may be torrent-only. Enable torrenting in **Settings** first.

---

## Summary

- **The Official Ascendara Index** provides the full Ascendara experience with automatic updates and full metadata
- **External Sources Mode** loads games from a supported source bucket, your own Custom Lists, or a self-hosted library
- Community sources have reduced metadata; self-hosted libraries support source-provided details, optional Steam matching, and custom artwork
- For the best overall experience, use the official Ascendara index
