# Ascendara API Overview

## Introduction
The Ascendara API is a RESTful interface for game discovery, download tracking, system health, and image assets. It is designed for non-commercial use and does not serve actual game files.

## API Basics

### Base URL
```
https://api.ascendara.app
```

### Response Format
All responses are JSON. Success and error response structures depend on the endpoint.

#### Example Success Responses
Health check:
```json
{
  "status": "healthy",
  "version": "5.5",
  "endpoint": "ascendara-api"
}
```
Download tracked:
```json
{
  "message": "Download tracked successfully"
}
```
Version info:
```json
{
  "version": "x.x.x"
}
```

#### Example Error Response
```json
{
  "error": "error_message"
}
```

### Rate Limiting
- Image endpoints: 30 requests per 60 seconds
- General endpoints: 3 requests per hour
- Rate limits are per IP
- Some endpoints have custom limits (see endpoint reference)

## Core Public Endpoints

### Health & Status
- `GET /health` — Returns API health and version
- `GET /` — Returns running version and status

### Game Data
- [`GET /json/games` — List all games with metadata](https://ascendara.app/docs/api-reference/games-list)
- [`GET /v2/image/{imageid}` — Get image for a specific game](https://ascendara.app/docs/api-reference/endpoints#get-game-image)
- [`GET /v2/image?place={position}` — Get game image by popularity weight](https://ascendara.app/docs/api-reference/endpoints#get-game-image-by-weight)

### Statistics
- [`GET /stats/downloads` — Download counts for all games](https://ascendara.app/docs/api-reference/endpoints#get-download-statistics)
- [`POST /stats/download` — Track a download event](https://ascendara.app/docs/api-reference/endpoints#track-download)

### Information
- [`GET /json/changelog` — Changelog information](https://ascendara.app/docs/api-reference/endpoints#get-changelog)
- [`GET /public/json/current` — Current data version](https://ascendara.app/docs/api-reference/endpoints#get-current-app-version)
- [`GET /json/roadmap` — Roadmap information](https://ascendara.app/docs/api-reference/endpoints#get-roadmap)

### Language
- [`GET /language/version` — Current language file version](https://ascendara.app/docs/api-reference/endpoints#get-language-version)
- [`GET /language/en` — English translation file](https://ascendara.app/docs/api-reference/endpoints#get-language-translation)

### Other
- [`GET /virustotal/latest` — Latest VirusTotal results](https://ascendara.app/docs/api-reference/endpoints#get-virustotal-results)
- [`GET /average-app-rating` — App average rating](https://ascendara.app/docs/api-reference/endpoints#get-average-app-rating)
- [`GET /opencritic/game?name={game}` — OpenCritic info for a game](endpoints.md#get-opencritic-game-info)
- [`POST /site/feedback` — Submit feedback (rate limited)](endpoints.md#submit-feedback)

### Error Handling
All endpoints return errors in the format:
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