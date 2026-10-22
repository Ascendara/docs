# Ascendara API Overview

The Ascendara API at `api.ascendara.app` powers the Ascendara app. Its public endpoints give you read access to the official game index, game metadata and update checks, community ratings and view counts, release information, and a few other data sources. You can use them in your own non-commercial projects.

The API never hosts game files. Download links in the index point to third-party file hosts.

## Basics

| | |
| --- | --- |
| **Base URL** | `https://api.ascendara.app` |
| **Format** | JSON, except image endpoints (which return the image) |
| **Authentication** | None for the endpoints documented here |
| **Usage** | Non-commercial use only |

```bash
curl https://api.ascendara.app/
```

```json
{
  "appVer": "11.5.3",
  "status": "OK",
  "readme": "This is the public API for Ascendara. Only available for non-commercial use",
  "documentation": "https://ascendara.app/docs/api-reference/overview"
}
```

## Fair Use

- **Cache what you download.** The full game list is several megabytes. It supports `ETag`/`If-None-Match`, so a repeat request costs almost nothing when the list hasn't changed. The index is refreshed on a schedule; polling it more than every few minutes gains you nothing.
- **Batch when you can.** To check updates for many games, use [`POST /v3/games/checkupdates`](/docs/api-reference/endpoints#check-updates-for-many-games) instead of one request per game.
- **Expect rate limits.** Limits are per IP address. Going over returns `429` (see [Error Handling](/docs/api-reference/error-handling)). IPs that keep abusing the API can be blocked.
- **Don't rely on undocumented endpoints.** Ascendara also uses endpoints that need signed requests from official builds or a signed-in account (reports, ratings, Ascend, cloud backups, index uploads). Those can change at any time and aren't covered here.

## Rate Limits

| Endpoints | Limit per IP |
| --- | --- |
| Images (`/v2/image/...`) | 30 per minute |
| `POST /v3/games/checkupdates` | 60 per minute |
| `GET /app/v1/gameviews` | 30 per minute |
| `POST /site/feedback` | 2 per minute |
| Everything else documented here | No fixed limit. Be reasonable |

## Endpoints

### App and Status

| Endpoint | Returns |
| --- | --- |
| [`GET /`](/docs/api-reference/endpoints#latest-app-version) | Latest Ascendara version |
| [`GET /branch-versions`](/docs/api-reference/endpoints#branch-versions) | Latest version on each release branch |
| [`GET /app/brokenversions`](/docs/api-reference/endpoints#broken-versions) | Versions with known critical issues |
| [`GET /downloader-version`](/docs/api-reference/endpoints#downloader-version) | Current download manager version |
| [`GET /health`](/docs/api-reference/endpoints#health) | API status and version |

### Game Index

| Endpoint | Returns |
| --- | --- |
| [`GET /json/v2/games`](/docs/api-reference/games-list) | The full game list |
| [`GET /v3/game/{gameID}`](/docs/api-reference/endpoints#game-name) | A game's name |
| [`GET /v3/game/checkupdate/{gameID}`](/docs/api-reference/endpoints#check-a-game-for-updates) | Whether a game has an update |
| [`POST /v3/games/checkupdates`](/docs/api-reference/endpoints#check-updates-for-many-games) | Update checks for up to 200 games |
| [`GET /json/v2/timemachine/{gameID}`](/docs/api-reference/endpoints#version-history) | Every version of a game the index has seen |
| [`GET /v2/image/{imgID}`](/docs/api-reference/endpoints#cover-image) | A game's cover image |
| [`GET /v2/image?place={rank}`](/docs/api-reference/endpoints#cover-image-by-popularity) | Cover of the game at a popularity rank |
| [`GET /v3/verifiedgames`](/docs/api-reference/endpoints#verified-games) | IDs of Verified games |
| [`GET /json/featured-game`](/docs/api-reference/endpoints#featured-game) | The featured game |
| [`GET /localindex/status`](/docs/api-reference/endpoints#index-status) | Whether the index is refreshing normally |
| [`GET /localindex/info`](/docs/api-reference/endpoints#index-info) | Date, size, and game count of the index |

### Community Data

| Endpoint | Returns |
| --- | --- |
| [`GET /app/v2/gameratings`](/docs/api-reference/endpoints#all-game-ratings) | Average rating for every rated game |
| [`GET /app/v2/gamerating/{gameID}`](/docs/api-reference/endpoints#game-rating-and-reviews) | One game's rating and reviews |
| [`GET /app/v1/gameviews`](/docs/api-reference/endpoints#game-views) | View counts per game |
| [`GET /stats/downloads`](/docs/api-reference/endpoints#download-counts) | Download counts per game |
| [`GET /average-app-rating`](/docs/api-reference/endpoints#app-rating) | Ascendara's average rating |

### File Hosts

| Endpoint | Returns |
| --- | --- |
| [`GET /app/json/providers`](/docs/api-reference/endpoints#provider-patterns) | URL patterns for each supported file host |
| [`GET /app/json/torboxstatus`](/docs/api-reference/endpoints#provider-status) | Online/offline status of each file host |

### Releases and Content

| Endpoint | Returns |
| --- | --- |
| [`GET /json/changelog/v2`](/docs/api-reference/endpoints#changelog) | Changelog |
| [`GET /json/news`](/docs/api-reference/endpoints#news) | News posts |
| [`GET /json/roadmap`](/docs/api-reference/endpoints#roadmap) | Roadmap |
| [`GET /app/earlychanges/read`](/docs/api-reference/endpoints#experimental-features) | Experimental features and votes |
| [`GET /json/publicthemes`](/docs/api-reference/endpoints#community-themes) | Community themes |
| [`GET /virustotal/latest`](/docs/api-reference/endpoints#virustotal-results) | Latest VirusTotal scans |
| [`GET /language/version`](/docs/api-reference/endpoints#language-version) | Language file version |
| [`GET /language/en`](/docs/api-reference/endpoints#english-language-file) | English language file |
| [`POST /site/feedback`](/docs/api-reference/endpoints#submit-feedback) | Submits feedback |

## Related

- [Endpoints](/docs/api-reference/endpoints): Every public endpoint in detail
- [Game List](/docs/api-reference/games-list): The game list format
- [Error Handling](/docs/api-reference/error-handling): Error responses and status codes
- [Monitor API](/docs/api-reference/monitor-api): The separate service behind the Companion webview
