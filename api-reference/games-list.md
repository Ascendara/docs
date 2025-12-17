# /json/games — Game List Endpoint

This page provides detailed documentation for the `/json/games` endpoint, including:
- Endpoint usage and purpose
- Example response (with empty/default values)
- Full explanation of all game and metadata keys

---

## Endpoint

**GET** `/json/games`

Returns a list of all available games with metadata and system requirements.

---

## Example Response

```json
{
  "games": [
    {
      "category": [],
      "dirlink": "",
      "dlc": false,
      "download_links": {},
      "game": "",
      "imgID": "",
      "latest_update": "",
      "minReqs": {
        "cpu": "",
        "directx": "",
        "gpu": "",
        "os": "",
        "ram": "",
        "storage": ""
      },
      "online": false,
      "rating": 0,
      "size": "",
      "version": "",
      "weight": ""
    }
  ],
  "metadata": {
    "apiVersion": "2.5",
    "games": "3557",
    "getDate": "November 25, 2025, 07:34 PM",
    "source": "STEAMRIP"
  }
}
```

---

## Game Object Keys

| Key            | Type                | Description                                                                 |
|----------------|---------------------|-----------------------------------------------------------------------------|
| category       | array of strings    | List of genres/categories for the game                                      |
| dirlink        | string (URL)        | Direct link to the original game page (external)                            |
| dlc            | boolean             | Whether the game is a DLC (true/false)                                      |
| download_links | object              | Download links by source (see below)                                        |
| game           | string              | Game title/name                                                             |
| imgID          | string              | Image ID for referencing game images                                        |
| latest_update  | string (date)       | Date of the latest update (YYYY-MM-DD)                                      |
| minReqs        | object              | Minimum system requirements (see minReqs table below)                       |
| online         | boolean             | Whether the game has online/multiplayer features                            |
| rating         | number              | Average user rating (0–5, or 0 if no ratings)                               |
| size           | string              | Download size (e.g. "21.1 GB")                                              |
| version        | string/number       | Internal version identifier                                                 |
| weight         | string/number       | Popularity weight (higher = more popular)                                   |

### minReqs Object

| Key      | Type   | Description                        |
|----------|--------|------------------------------------|
| cpu      | string | Minimum CPU                        |
| directx  | string | Minimum DirectX version            |
| gpu      | string | Minimum GPU                        |
| os       | string | Required OS                        |
| ram      | string | Required RAM                       |
| storage  | string | Required disk space                |

### download_links Object

- Each key (e.g. `buzzheavier`, `gofile`) is a source name. The value is an array of download URLs for that source.

---

## Metadata Object Keys

| Key         | Type    | Description                                             |
|-------------|---------|--------------------------------------------------------|
| apiVersion  | string  | API version of the dataset                             |
| games       | string/number | Total number of games in the dataset                |
| getDate     | string  | Date and time when this data was generated (human readable) |
| source      | string  | Source of the game data (e.g. "STEAMRIP")            |

---

## Notes
- The `metadata` object may contain additional info about the dataset (fields, version, etc).
- The games array may be empty if no games are available.
