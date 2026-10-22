# API Endpoints

Every public endpoint of `https://api.ascendara.app`. None of them need authentication. See the [overview](/docs/api-reference/overview) for rate limits and fair use.

Examples are trimmed for length, and your values will differ.

---

## App and Status

### Latest App Version

`GET /`

The latest Ascendara version on the Live branch. Ascendara uses it to check for updates, and the Linux install script uses it to find the latest AppImage.

```json
{
  "appVer": "11.5.3",
  "status": "OK",
  "readme": "This is the public API for Ascendara. Only available for non-commercial use",
  "documentation": "https://ascendara.app/docs/api-reference/overview"
}
```

### Branch Versions

`GET /branch-versions`

The latest version on each release branch.

```json
{
  "live": "11.5.3",
  "public-testing": "11.5.3",
  "experimental": "11.5.3"
}
```

### Broken Versions

`GET /app/brokenversions`

Versions with known critical issues. Ascendara shows a warning when it's running one of them.

```json
["7.8.2", "9.0.2", "9.1.5"]
```

### Downloader Version

`GET /downloader-version`

The current version of the [Download Manager](/docs/binary-tool/downloader).

```json
{ "version": "4.0.1" }
```

### Health

`GET /health`

The API's status and version. `status` is `"updatingIndex"` while the index is being rebuilt and `"healthy"` otherwise.

```json
{
  "status": "healthy",
  "version": "5.5",
  "endpoint": "ascendara-api"
}
```

---

## Game Index

### Game List

`GET /json/v2/games`

Every game in the official index, with metadata, download links, and system requirements. This is the list Ascendara syncs into each user's local index. It supports `ETag`/`If-None-Match` caching.

See [Game List](/docs/api-reference/games-list) for the full format.

### Game Name

`GET /v3/game/{gameID}`

Looks up a game's name by its `gameID`. Returns `404` when the ID isn't in the current index.

```json
{ "gameID": "AtruRq", "name": "Felvidek" }
```

### Check a Game for Updates

`GET /v3/game/checkupdate/{gameID}?local_version={version}`

Compares the version you have with the version in the index.

| Parameter | In | Description |
| --- | --- | --- |
| `gameID` | path | The game's `gameID` |
| `local_version` | query | The installed version. Optional. |

```json
{
  "gameID": "AtruRq",
  "gameName": "Felvidek",
  "latestVersion": "18709219",
  "localVersion": "0.1",
  "updateAvailable": true,
  "autoUpdateSupported": true,
  "downloadLinks": {
    "buzzheavier": ["https://bzzhr.to/..."]
  }
}
```

- `updateAvailable` is `true` when the two versions differ (case-insensitive). An empty or `"-1"` version on either side counts as unknown, so it never reports an update.
- `autoUpdateSupported` is `true` when the game has a link on a provider Ascendara can download automatically (`gofile`, `buzzheavier`, or `pixeldrain`).
- `downloadLinks` is empty unless an update is available.

### Check Updates for Many Games

`POST /v3/games/checkupdates`

The batch version of the endpoint above. Use it instead of checking games one at a time.

**Rate limit:** 60 per minute

**Body** (up to 200 games):

```json
{
  "games": [
    { "gameID": "AtruRq", "version": "1.0" },
    { "gameID": "zzzzzz", "version": "1" }
  ]
}
```

**Response:** `results` maps each found `gameID` to the same object as the single check. IDs that aren't in the index are listed in `notFound`.

```json
{
  "results": {
    "AtruRq": {
      "gameID": "AtruRq",
      "gameName": "Felvidek",
      "latestVersion": "18709219",
      "localVersion": "1.0",
      "updateAvailable": true,
      "autoUpdateSupported": true,
      "downloadLinks": { "buzzheavier": ["https://bzzhr.to/..."] }
    }
  },
  "notFound": ["zzzzzz"]
}
```

Returns `400` if `games` isn't an array and `413` if it has more than 200 entries.

### Version History

`GET /json/v2/timemachine/{gameID}`

Every version of a game the index has seen, newest first. This powers Ascendara's **Time Machine**, which lets users download an older version.

```json
{
  "gameID": "AtruRq",
  "currentVersion": "18709219",
  "releases": [
    {
      "version": "18709219",
      "firstSeen": "2026-10-14T17:07:56Z",
      "lastSeen": null,
      "live": true,
      "game": { "game": "Felvidek", "version": "18709219", "download_links": { } }
    }
  ]
}
```

- `live` marks the version that's in the index now. Its `game` comes from the current index, so its links are up to date. Older entries hold the game object as it was then, in the [game list format](/docs/api-reference/games-list#game-fields).
- `lastSeen` is `null` for the live version.
- Supports `ETag` caching. Returns `503` while the history is being rebuilt.

### Cover Image

`GET /v2/image/{imgID}`

A game's cover image. Use the `imgID` from the game list. Returns `404` if there's no image.

**Rate limit:** 30 per minute (shared by all image endpoints)

### Cover Image by Popularity

`GET /v2/image?place={rank}`

The cover of the game at a popularity rank (by `weight`), starting at `1` for the most popular.

**Rate limit:** 30 per minute (shared by all image endpoints)

### Verified Games

`GET /v3/verifiedgames`

The `gameID`s that show the **Verified Game** badge.

```json
["AeDxvd", "ABsKrW", "AJZuXd"]
```

### Featured Game

`GET /json/featured-game`

The game currently featured in Ascendara.

```json
{ "gameId": "ApAHKG" }
```

### Index Status

`GET /localindex/status`

Whether the API is keeping the index up to date.

```json
{
  "success": true,
  "serverRefresh": true,
  "blocked": false,
  "blockedSince": null,
  "lastChecked": "2026-10-22T15:57:03Z",
  "lastSuccess": "2026-10-22T15:57:03Z",
  "lastChanged": "2026-10-22T15:57:18.839459Z"
}
```

| Field | Meaning |
| --- | --- |
| `serverRefresh` | The API refreshes the index on its own schedule |
| `blocked` | The game source is blocking the API. When this is `true` and the index is stale, Ascendara asks users to [help refresh](/docs/features/refreshing-index#helping-refresh-the-index). |
| `blockedSince` | When the block started |
| `lastChecked` | The last refresh attempt |
| `lastSuccess` | The last successful refresh |
| `lastChanged` | The last time the game list actually changed |

### Index Info

`GET /localindex/info`

The date, size in bytes, and game count of the current shared index.

```json
{
  "success": true,
  "date": "2026-10-22T15:57:18.334615Z",
  "gameCount": 4963,
  "size": 339194010
}
```

---

## Community Data

### All Game Ratings

`GET /app/v2/gameratings`

The average rating (out of 5) and number of ratings for every rated game, keyed by `gameID`.

```json
{
  "AAzQgC": { "gameName": "The Binding of Isaac: Repentance", "rating": 4.3, "ratingCount": 7 },
  "ABBDDE": { "gameName": "Euro Truck Simulator 2", "rating": 3.3, "ratingCount": 3 }
}
```

### Game Rating and Reviews

`GET /app/v2/gamerating/{gameID}`

One game's rating with its individual reviews, newest first. A game with no ratings returns `rating: 0` and an empty `reviews` list.

```json
{
  "gameName": "The Binding of Isaac: Repentance",
  "rating": 4.3,
  "ratingCount": 7,
  "reviews": [
    {
      "id": "1502731943647641701",
      "rating": 5,
      "comments": "",
      "username": "Anonymous",
      "photoURL": null,
      "isAscendAccount": null,
      "createdAt": "2026-05-09T18:00:14.775Z"
    }
  ]
}
```

### Game Views

`GET /app/v1/gameviews`

How many unique viewers have seen each game's card in Ascendara, keyed by `gameID`. Each viewer counts once per game. The response is cached for 60 seconds.

**Rate limit:** 30 per minute

```json
{ "AAkCPR": 78, "AAxEbq": 312, "ABCErC": 430 }
```

### Download Counts

`GET /stats/downloads`

How many times each game has been downloaded through Ascendara, keyed by game name, plus a `total`.

```json
{
  "total": 67582,
  "007 First Light": 94,
  "60 Seconds!": 4
}
```

### App Rating

`GET /average-app-rating`

Ascendara's average rating and the number of ratings.

```json
{ "average_rating": 4.7, "total_count": 817 }
```

---

## File Hosts

### Provider Patterns

`GET /app/json/providers`

The URL patterns Ascendara uses to recognize each file host. `regex` matches a provider's direct download URL, and `forwardingLinks` matches the pages it forwards from.

```json
{
  "lastUpdated": "2026-08-16",
  "forwardingLinks": {
    "gofile": "https:\\/\\/gofile.io",
    "pixeldrain": "https:\\/\\/pixeldrain.com"
  },
  "regex": {
    "gofile": "^https:\\/\\/store\\d*\\.gofile\\.io\\/download\\/web\\/[a-f0-9-]+\\/[\\w\\s\\.-]+\\.(zip|rar|7z)$"
  }
}
```

### Provider Status

`GET /app/json/torboxstatus`

Whether each file host is currently `online` or `offline`.

```json
{
  "buzzheavier": "online",
  "datanodes": "online",
  "gofile": "online",
  "megadb": "offline",
  "vikingfile": "online"
}
```

---

## Releases and Content

### Changelog

`GET /json/changelog/v2`

The changelog Ascendara shows in **What's New**.

```json
{
  "version": "...",
  "contributors": ["..."],
  "entries": [
    {
      "version": "11.5.3",
      "date": "2026-10-20",
      "title": "...",
      "description": "...",
      "features": [],
      "major": false,
      "release": "..."
    }
  ]
}
```

The older format is still available at `GET /json/changelog`.

### News

`GET /json/news`

News posts from the Ascendara site. Each post has `slug`, `title`, `date`, `category`, `excerpt`, `tags`, `readTime`, `featured`, `version`, and a `body` made of content blocks. `categories` maps category IDs to display names.

```json
{
  "version": "...",
  "categories": { "announcement": "Announcement", "feature": "Feature", "milestone": "Milestone", "release": "Release" },
  "posts": [
    { "slug": "...", "title": "...", "date": "...", "category": "release", "excerpt": "...", "body": [] }
  ]
}
```

### Roadmap

`GET /json/roadmap`

Completed and planned features. Entries may credit a `contributor`.

```json
{
  "completed": [
    { "feature": "Linux support" },
    { "feature": "Persist filters during session", "contributor": "Gab4545" }
  ]
}
```

### Experimental Features

`GET /app/earlychanges/read`

Features being tested on the experimental branch, with their votes. `GET /app/earlychanges/version` returns only the experimental build version.

```json
{
  "earlyBuild": "8.4.0-2",
  "releasedVersion": "8.3.9",
  "changes": [
    { "id": 1, "title": "Home Page Redesign", "description": "...", "status": "active", "thumbsup": 0, "thumbsdown": 0 }
  ]
}
```

### Community Themes

`GET /json/publicthemes`

The themes in **Settings** → **Community Themes**. Each theme's `colors` holds space-separated RGB values for each theme color.

```json
[
  {
    "name": "...",
    "author": "Kacper",
    "themeType": "dark",
    "version": 1,
    "colors": { "background": "23 23 23", "primary": "150 150 150", "accent": "17 17 17" }
  }
]
```

### VirusTotal Results

`GET /virustotal/latest`

The latest VirusTotal scans of Ascendara's installer and files.

```json
[
  {
    "file_name": "AscendaraInstaller.exe",
    "positives": 1,
    "scan_date": "2025-11-27 18:55:36 UTC",
    "scan_id": "..."
  }
]
```

### Language Version

`GET /language/version`

The current version of the language files. Ascendara uses it to know when extra languages need to be translated again.

```json
{ "version": "35" }
```

### English Language File

`GET /language/en`

The English language file, which the [Language Translator](/docs/binary-tool/language-translator) translates into extra languages.

```json
{
  "account": {
    "actions": "Account Actions"
  }
}
```

### Submit Feedback

`POST /site/feedback`

Sends feedback to the Ascendara team. `type` is `feature`, `improvement`, `question`, or `other`. `email` is optional.

**Rate limit:** 2 per minute

```json
{
  "type": "feature",
  "subject": "...",
  "message": "...",
  "email": "optional@example.com"
}
```

```json
{ "status": "success", "message": "Feedback submitted successfully" }
```

---

## Deprecated

| Endpoint | Status |
| --- | --- |
| `GET /json/games` | Frozen. Returns an old list in the previous format, with `"warn": "This endpoint is deprecated"`. Use `/json/v2/games`. |
| `GET /image/{id}`, `GET /image` | Removed. Use `/v2/image/{imgID}`. |
| `GET /public/json/current` | Legacy placeholder. Use `GET /` for the app version. |
| `GET /json/timemachine/game/{name}` | Name-based version history for older clients. Use `/json/v2/timemachine/{gameID}`. |
| `GET /opencritic/game?name={game}` | No longer used by Ascendara. |

## App-Only Endpoints

Ascendara uses some endpoints that don't need authentication but aren't meant for other projects. They are undocumented on purpose and can change at any time:

- **`/api/proxy/steam/*` and `/api/proxy/steamgrid/*`**: proxies to the Steam Store and SteamGridDB that use Ascendara's own API keys. For your own project, use [SteamGridDB](https://www.steamgriddb.com/api/v2) and the Steam API directly with your own key.
- **`POST /stats/download`** and **`POST /app/track/error`**: Ascendara's usage and error reporting.
- **`/api/sources/bucket`**: Resolves the source buckets Ascendara recognizes.
