# Ascendara API Overview

## Introduction
The Ascendara API is a RESTful interface for game discovery, the game index, download statistics, app versions, and image assets. It's designed for non-commercial use and never serves game files.

## API Basics

### Base URL
```
https://api.ascendara.app
```

### Response Format
All responses are JSON, except image endpoints. Success and error response structures depend on the endpoint.

#### Example Success Responses
Health check:
```json
{
  "status": "healthy",
  "version": "5.5",
  "endpoint": "ascendara-api"
}
```
App version:
```json
{
  "appVer": "11.4.0",
  "status": "OK",
  "readme": "This is the public API for Ascendara. Only available for non-commercial use",
  "documentation": "https://ascendara.app/docs/api-reference/overview"
}
```

#### Example Error Response
```json
{
  "error": "error_message"
}
```

### Authentication
The public endpoints below don't need authentication. Other endpoints used by the app (reports, ratings, Ascend, the index relay) require signed requests from official Ascendara builds and aren't documented here.

### Rate Limiting
- Image endpoints: 30 requests per 60 seconds
- Some endpoints have their own limits (see the endpoint reference)
- Rate limits are per IP

## Core Public Endpoints

### Health & Status
- [`GET /health` — API health and version](/docs/api-reference/endpoints#get-health-status)
- [`GET /` — Latest Ascendara version](/docs/api-reference/endpoints#get-current-app-version)
- [`GET /branch-versions` — Latest version on each release branch](/docs/api-reference/endpoints#get-branch-versions)
- [`GET /app/brokenversions` — Versions with known critical issues](/docs/api-reference/endpoints#get-broken-versions)

### Game Index
- [`GET /json/v2/games` — The official game list](/docs/api-reference/games-list)
- [`GET /localindex/status` — Whether the index is refreshing normally](/docs/api-reference/endpoints#get-index-status)
- [`GET /localindex/info` — Index date and game count](/docs/api-reference/endpoints#get-index-info)
- [`GET /v3/verifiedgames` — Verified game IDs](/docs/api-reference/endpoints#get-verified-games)
- [`GET /v2/image/{imageid}` — Image for a specific game](/docs/api-reference/endpoints#get-game-image)
- [`GET /v2/image?place={position}` — Image by popularity rank](/docs/api-reference/endpoints#get-game-image-by-weight)
- [`GET /json/games` — Legacy game list (deprecated)](/docs/api-reference/games-list#legacy-endpoint)

### Statistics
- [`GET /stats/downloads` — Download counts for all games](/docs/api-reference/endpoints#get-download-statistics)
- [`GET /average-app-rating` — Average app rating](/docs/api-reference/endpoints#get-average-app-rating)

### Information
- [`GET /json/changelog/v2` — Changelog](/docs/api-reference/endpoints#get-changelog)
- [`GET /json/roadmap` — Roadmap](/docs/api-reference/endpoints#get-roadmap)
- [`GET /json/publicthemes` — Community themes](/docs/api-reference/endpoints#get-community-themes)
- [`GET /virustotal/latest` — Latest VirusTotal results](/docs/api-reference/endpoints#get-virustotal-results)

### Language
- [`GET /language/version` — Current language file version](/docs/api-reference/endpoints#get-language-version)
- [`GET /language/en` — English language file](/docs/api-reference/endpoints#get-language-translation)

### Feedback
- [`POST /site/feedback` — Submit feedback](/docs/api-reference/endpoints#submit-feedback)

### Error Handling
All endpoints return errors in this format:
```json
{
  "error": "error_message"
}
```

Common HTTP status codes:
- 200: Success
- 400: Bad Request
- 404: Not Found
- 429: Rate Limit Exceeded
- 500: Server Error

See [Error Handling](/docs/api-reference/error-handling).
