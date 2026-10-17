# External Sources and Game Index

Ascendara keeps a game index on your computer for fast browsing and offline access. By default it uses the official Ascendara index. External Sources Mode lets you load games from a community source bucket or from JSON lists you import yourself instead.

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

This mode is designed for flexibility and custom sourcing. You can point Ascendara at a supported source bucket and pick one of its sources, or import your own JSON lists.

**Features**

- Supports external source bucket URLs
- Lets you choose your own source, and switch between sources you've used
- Supports your own imported JSON lists (Custom Lists)
- Flexible for custom or community-maintained sources

**Limitations**

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

## Custom Lists

Custom Lists let you import your own game JSON and use it as your source.

1. On the **External Sources** page, with **Use external sources** turned on, select **Import New List** under **Custom Lists**.
2. Paste the JSON, or select **Paste from clipboard**, and click **Process JSON**.
3. Review the detected structure, keys, and number of items, then give the list a name.
4. Import the list. It becomes the active list.

Select a list to switch to it. You can also rename or delete lists. Lists are stored as JSON files in `Documents/Ascendara/CustomLists`.

---

## Supported Formats

External sources and Custom Lists accept two JSON structures.

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

When this structure is used as an external source, Ascendara reads each game's name (`game`, `title`, or `name`), its links (`download_links`, `dirlink`, `url`, `uri`, `uris`, or `magnet`), its size (`size` or `fileSize`), and its date (`latest_update`, `uploadDate`, or `date`). Other fields such as categories, ratings, and images are not used in External Sources Mode. See the [game list format](/docs/api-reference/games-list) for all fields of the official index.

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

Sources don't provide cover images. Ascendara tries to find artwork for each game from SteamGridDB by matching the title. Results depend on how closely the title matches, so some games may have incorrect or missing artwork.

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
- This is expected in External Sources Mode. For categories, ratings, popularity sorting, and covers, switch back to the official Ascendara index.

### I can't select a source
- The source may be torrent-only. Enable torrenting in **Settings** first.

---

## Summary

- **The Official Ascendara Index** provides the full Ascendara experience with automatic updates and full metadata
- **External Sources Mode** loads games from a supported source bucket or your own Custom Lists
- External sources are more flexible, but come with reduced metadata and fewer browsing features
- For the best overall experience, use the official Ascendara index
