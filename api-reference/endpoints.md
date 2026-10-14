# API Endpoints Reference

All endpoints are relative to `https://api.ascendara.app`.

---

## Health & Status

### Get Health Status
**GET `/health`**

Returns the API's health and version.

**Response:**
```json
{
  "status": "healthy",
  "version": "5.5",
  "endpoint": "ascendara-api"
}
```
- `status`: `"healthy"` or `"updatingIndex"`
- `version`: API version
- `endpoint`: Service name

**Rate Limit:** 3/hour per IP

---

### Get Current App Version
**GET `/`**

Returns the latest version of Ascendara on the Live branch. Ascendara uses this to check for updates, and the Linux install script uses it to download the latest AppImage.

**Response:**
```json
{
  "appVer": "11.4.0",
  "status": "OK",
  "readme": "This is the public API for Ascendara. Only available for non-commercial use",
  "documentation": "https://ascendara.app/docs/api-reference/overview"
}
```

---

### Get Branch Versions
**GET `/branch-versions`**

Returns the latest version on each release branch.

**Response:**
```json
{
  "live": "11.4.0",
  "public-testing": "11.4.0",
  "experimental": "11.4.0"
}
```

---

### Get Broken Versions
**GET `/app/brokenversions`**

Returns the versions of Ascendara with known critical issues. Ascendara shows a warning when it's running one of them.

**Response:**
```json
["7.8.2", "9.0.2", "9.1.5"]
```

---

## Game Index

### Get Games List
**GET `/json/v2/games`**

Returns the official game list with metadata. This is the list Ascendara syncs into each user's local index. The response includes an `ETag` header, so clients can send `If-None-Match` and receive `304 Not Modified` when nothing changed.

See the [Game List Endpoint](/docs/api-reference/games-list) for the full response format.

---

### Get Index Status
**GET `/localindex/status`**

Returns whether the API is keeping the game index up to date.

**Response:**
```json
{
  "success": true,
  "serverRefresh": true,
  "blocked": false,
  "blockedSince": null,
  "lastSuccess": "2026-10-14T20:21:39Z",
  "lastChecked": "2026-10-14T20:21:39Z",
  "lastChanged": "2026-10-14T20:22:20.425910Z"
}
```
- `serverRefresh`: The API refreshes the index on a schedule
- `blocked`: The game source is currently blocking the API. When this is `true` and the index is stale, Ascendara asks users to [help refresh](/docs/features/refreshing-index#helping-refresh-the-index).
- `lastSuccess`: The last successful refresh
- `lastChanged`: The last time the game list changed

---

### Get Index Info
**GET `/localindex/info`**

Returns information about the current shared index.

**Response:**
```json
{
  "success": true,
  "date": "2026-10-14T20:53:05.818553Z",
  "gameCount": 4929,
  "size": 337393746
}
```

---

### Get Verified Games
**GET `/v3/verifiedgames`**

Returns the game IDs that show the **Verified Game** badge.

**Response:**
```json
["AeDxvd", "ABsKrW", "AJZuXd"]
```

---

### Get Game Image
**GET `/v2/image/{imageid}`**

Returns the cover image for a game.

**Path Parameter:**
- `imageid` (string): The `imgID` from the game list

**Response:**
- The image file (JPEG)
- `404` if the image doesn't exist

**Rate Limit:** 30/min per IP

---

### Get Game Image by Weight
**GET `/v2/image?place={position}`**

Returns the image for the game at the given popularity rank.

**Query Parameter:**
- `place` (integer, required): 1-based ranking

**Response:**
- The image file (JPEG)
- An error if the place is invalid or not found

**Rate Limit:** 30/min per IP

---

## Statistics

### Get Download Statistics
**GET `/stats/downloads`**

Returns download counts for every game, plus the total.

**Response:**
```json
{
  "total": 67582,
  "Game Name 1": 12,
  "Game Name 2": 4
}
```

---

### Get Average App Rating
**GET `/average-app-rating`**

Returns the average app rating and the number of ratings.

**Response:**
```json
{
  "average_rating": 4.7,
  "total_count": 817
}
```

---

## Information

### Get Changelog
**GET `/json/changelog/v2`**

Returns the changelog that Ascendara shows in **What's New**.

**Response:**
```json
{
  "version": "...",
  "contributors": ["..."],
  "entries": [
    {
      "version": "11.4.0",
      "date": "2026-10-15",
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

---

### Get Roadmap
**GET `/json/roadmap`**

Returns the roadmap, including completed and planned features.

---

### Get Community Themes
**GET `/json/publicthemes`**

Returns the community themes shown in **Settings** → **Community Themes**. Each theme has an `author` and a `colors` object with RGB values for each theme color.

---

### Get VirusTotal Results
**GET `/virustotal/latest`**

Returns the latest VirusTotal scan results for Ascendara's installer and files.

**Response:**
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

---

## Language

### Get Language Version
**GET `/language/version`**

Returns the current version of the language files. Ascendara uses it to know when extra languages need to be translated again.

**Response:**
```json
{
  "version": "35"
}
```

---

### Get Language Translation
**GET `/language/en`**

Returns the English language file, which the [Language Translator](/docs/binary-tool/language-translator) translates into extra languages.

**Response:**
```json
{
  "account": {
    "actions": "Account Actions"
  }
}
```

---

## Feedback

### Submit Feedback
**POST `/site/feedback`**

Submit feedback (feature, improvement, question, or other).

**Body:**
```json
{
  "type": "feature",
  "subject": "...",
  "message": "...",
  "email": "optional@example.com"
}
```

**Response:**
```json
{
  "status": "success",
  "message": "Feedback submitted successfully"
}
```

**Rate Limit:** 2/min per IP

---

## Deprecated Endpoints

| Endpoint | Status |
| --- | --- |
| `GET /json/games` | Deprecated. Returns an old, frozen game list with a `"warn": "This endpoint is deprecated"` field. Use `/json/v2/games`. |
| `GET /public/json/current` | Legacy. Returns a placeholder version. Use `GET /` for the app version. |
| `GET /opencritic/game?name={game}` | No longer used by Ascendara and currently returns an error. |
| `POST /stats/download` | Used by Ascendara builds to count downloads. Not intended for third-party use. |

---

## Error Handling
All endpoints may return errors as:
```json
{
  "error": "error_message"
}
```
- 400: Bad Request
- 404: Not Found
- 429: Rate Limit Exceeded
- 500: Server Error
