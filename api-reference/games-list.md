# Game List

`GET /json/v2/games` returns every game in the official index: its metadata, download links, and system requirements. Ascendara syncs this list into each user's local index, and the API refreshes it on a schedule.

Each user's local `ascendara_games.json` uses the same format. See [Your Game Index](/docs/features/refreshing-index).

## Caching

The full list is several megabytes, so please cache it instead of downloading it repeatedly. Responses include `ETag` and `Last-Modified` headers. Send the `ETag` back in `If-None-Match` to get an empty `304 Not Modified` when nothing has changed:

```bash
curl -H 'If-None-Match: "1f3abd80c4f8a97fb21bac0f37e3c45a"' https://api.ascendara.app/json/v2/games
```

The response is `Cache-Control: public, max-age=60`. Checking more often than every few minutes gains you nothing; `metadata.lastUpdated` tells you when the list last changed.

## Example Response

```json
{
  "metadata": {
    "getDate": "October 22, 2026, 03:57 PM",
    "local": true,
    "source": "local_refresh_upload",
    "listVersion": "1.0",
    "games": "4963",
    "lastUpdated": "2026-10-22T15:57:18.334615Z",
    "lastScrape": "2026-10-22T15:57:03Z",
    "timemachine": true
  },
  "games": [
    {
      "game": "We Were Here Tomorrow",
      "gameID": "AtyzYc",
      "imgID": "63e811de953a86c555de",
      "size": "8.2 GB",
      "version": "1.1.1.696",
      "releasedBy": "",
      "online": true,
      "dlc": false,
      "dirlink": "https://steamrip.com/we-were-here-tomorrow-free-download/",
      "download_links": {
        "buzzheavier": ["https://bzzhr.to/h0lfiyfk0tqe"]
      },
      "weight": "101",
      "category": ["Action", "Adventure", "Indie", "Multiplayer"],
      "latest_update": "2026-10-21",
      "minReqs": {
        "os": "Windows 11",
        "cpu": "AMD Ryzen 5 3600 or equivalent",
        "ram": "8 GB RAM",
        "gpu": "Nvidia GTX 1660Ti 6 GB or equivalent",
        "directx": "Version 12",
        "storage": "25 GB available space"
      },
      "hasHistory": true
    }
  ]
}
```

## Game Fields

| Key | Type | Description |
| --- | --- | --- |
| `game` | string | Game title |
| `gameID` | string | Unique 6-character ID (see [Game ID encoding](/docs/binary-tool/local-refresh#game-id-encoding)). Used by the other endpoints, blacklists, verified games, ratings, and `ascendara://game?<gameID>` links. |
| `imgID` | string | Cover image ID, for [`/v2/image/{imgID}`](/docs/api-reference/endpoints#cover-image) |
| `size` | string | Download size, for example `"21.1 GB"` |
| `version` | string | Version from the source. Some games use build numbers. |
| `releasedBy` | string | The release group or crack used, or `""` when unknown |
| `online` | boolean | Online multiplayer works through Online-Fix |
| `dlc` | boolean | All DLC is included |
| `dirlink` | string | The game's page on the source site |
| `download_links` | object | Download links grouped by provider (see below) |
| `weight` | string | Popularity (view count). Higher is more popular, and `"0"` means unknown. |
| `category` | string[] | Genres and categories |
| `latest_update` | string | Date of the latest update (`YYYY-MM-DD`) |
| `minReqs` | object or `null` | Minimum system requirements, or `null` when the source doesn't list them |
| `hasHistory` | boolean | Only present (`true`) when older versions are available from [Version History](/docs/api-reference/endpoints#version-history). When `metadata.timemachine` is `true`, a missing `hasHistory` means the game has no older versions. |
| `storyOnly` | boolean | Only present (`true`) when the game's online mode doesn't work, so only the single-player story is playable |

`hasHistory` and `storyOnly` are added when the list is served. They are not part of indexes shared back to the API.

### minReqs

| Key | Description |
| --- | --- |
| `os` | Required operating system |
| `cpu` | Minimum CPU |
| `ram` | Required memory |
| `gpu` | Minimum graphics card |
| `directx` | Minimum DirectX version |
| `storage` | Required disk space |

Any key may be missing when the source doesn't list it.

### download_links

Each key is a provider and each value is a list of URLs on that provider. Current providers include `buzzheavier`, `megadb`, `gofile`, `1fichier`, `fileditch`, `datanodes`, `pixeldrain`, `vikingfile`, and `qiwi`. To match links to providers in your own code, use [Provider Patterns](/docs/api-reference/endpoints#provider-patterns). [Provider Status](/docs/api-reference/endpoints#provider-status) shows which providers are online.

## Metadata Fields

| Key | Type | Description |
| --- | --- | --- |
| `lastUpdated` | string | When the list last changed (ISO 8601). Ascendara compares this with the local list to decide which is newer. |
| `lastScrape` | string | When the source was last scraped (ISO 8601) |
| `getDate` | string | When the list was generated, in a readable form |
| `games` | string | Number of games |
| `source` | string | How the list was produced |
| `local` | boolean | The list uses the [Local Refresh](/docs/binary-tool/local-refresh) format |
| `listVersion` | string | Format version |
| `timemachine` | boolean | `hasHistory` is reliable for every game |

The `metadata` object may gain new fields, so ignore keys you don't recognize.

## Notes

- When Ascendara saves the list locally, it removes games whose `gameID` is in the user's blacklist.
- `GET /json/games` is deprecated. It returns an old, frozen list in the previous format (with `rating` on each game and `apiVersion` in the metadata) plus a `"warn": "This endpoint is deprecated"` field. Use `/json/v2/games` instead.
