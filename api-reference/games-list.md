# /json/v2/games — Game List Endpoint

This page documents the game list endpoint, including:
- Endpoint usage and caching
- An example response
- Every game and metadata key

The same format is used for `ascendara_games.json` in each user's [local game index](/docs/features/refreshing-index).

---

## Endpoint

**GET** `/json/v2/games`

Returns every game in the official index with its metadata, download links, and system requirements. The list is refreshed by the Ascendara API on a schedule.

### Caching
Responses include an `ETag` header. Send it back in `If-None-Match` to receive `304 Not Modified` when the list hasn't changed:

```bash
curl -H 'If-None-Match: "0b71fcb89c6351042cbaeefadeab4e42"' https://api.ascendara.app/json/v2/games
```

The full list is a few megabytes, so please cache it rather than downloading it repeatedly.

---

## Example Response

```json
{
  "metadata": {
    "getDate": "October 14, 2026, 08:22 PM",
    "local": true,
    "source": "local_refresh_upload",
    "listVersion": "1.0",
    "games": "4923",
    "lastUpdated": "2026-10-14T20:22:19.700465Z",
    "lastScrape": "2026-10-14T20:21:39Z"
  },
  "games": [
    {
      "game": "Example Game",
      "size": "580.9 MB",
      "version": "1.0.2",
      "releasedBy": "Goldberg",
      "online": false,
      "dlc": false,
      "dirlink": "https://steamrip.com/example-game-free-download/",
      "download_links": {
        "buzzheavier": ["https://bzzhr.to/example"],
        "gofile": ["https://gofile.io/d/example"]
      },
      "weight": "1520",
      "imgID": "a7535b9a0fad4b1894b7",
      "gameID": "AtruRq",
      "category": ["Indie", "Role-playing Game"],
      "latest_update": "2026-10-14",
      "minReqs": {
        "os": "Windows 10 64-bit",
        "cpu": "Intel Core i5",
        "ram": "8 GB RAM",
        "gpu": "NVIDIA GeForce GTX 1060",
        "directx": "Version 11",
        "storage": "10 GB available space"
      }
    }
  ]
}
```

---

## Game Object Keys

| Key            | Type                | Description |
|----------------|---------------------|-------------|
| game           | string              | Game title |
| gameID         | string              | Unique 6-character game ID (see [Game ID encoding](/docs/binary-tool/local-refresh#game-id-encoding)). Used for blacklists, verified games, ratings, and `ascendara://game?<gameID>` links. |
| imgID          | string              | Cover image ID, used with `/v2/image/{imgID}` |
| size           | string              | Download size (for example, `"21.1 GB"`) |
| version        | string              | Version string from the source. Some entries use build numbers. |
| releasedBy     | string              | The release group or crack used, when known |
| online         | boolean             | Online multiplayer is enabled through Online-Fix |
| dlc            | boolean             | All DLC is included |
| dirlink        | string (URL)        | The game's page on the source site |
| download_links | object              | Download links grouped by provider (see below) |
| weight         | string              | Popularity weight (view count). Higher is more popular; `"0"` if unknown. |
| category       | array of strings    | Genres and categories |
| latest_update  | string (date)       | Date of the latest update (`YYYY-MM-DD`) |
| minReqs        | object or null      | Minimum system requirements, or `null` when the source doesn't list them |

### minReqs Object

| Key      | Type   | Description |
|----------|--------|-------------|
| os       | string | Required OS |
| cpu      | string | Minimum CPU |
| ram      | string | Required RAM |
| gpu      | string | Minimum GPU |
| directx  | string | Minimum DirectX version |
| storage  | string | Required disk space |

Any key may be missing if the source doesn't list it.

### download_links Object

Each key is a provider name, and its value is an array of URLs for that provider. Providers include `gofile`, `buzzheavier`, `pixeldrain`, `megadb`, `qiwi`, `datanodes`, `1fichier`, `vikingfile`, and `fileditch`.

---

## Metadata Object Keys

| Key         | Type    | Description |
|-------------|---------|-------------|
| getDate     | string  | When the list was generated (human readable) |
| lastUpdated | string  | When the list was last updated (ISO 8601). Ascendara compares this with a local list to decide which is newer. |
| lastScrape  | string  | When the source was last scraped (ISO 8601) |
| games       | string  | Number of games in the list |
| source      | string  | How the list was produced |
| local       | boolean | Whether the list was built with the Local Refresh tool format |
| listVersion | string  | Format version of the list |

---

## Legacy Endpoint

**GET** `/json/games` is deprecated. It returns an old, frozen list in the previous format (with `rating` on each game and `apiVersion` in the metadata) and a `"warn": "This endpoint is deprecated"` field. Use `/json/v2/games` instead.

---

## Notes
- The `games` array is the only part of the response Ascendara needs to browse games. The `metadata` object may gain new fields.
- Ascendara removes games whose `gameID` is in the user's blacklist when it saves the list locally.
