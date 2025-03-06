# API Endpoints Reference

## Health Check

### Get Health Status
```http
GET /health
```
Returns system health status and version information.

Response:
```json
{
  "status": "healthy",
  "version": "5.5",
  "endpoint": "ascendara-api"
}
```

#### About Ascendara Status

This endpoint also returns the status of Ascendara's index, which can be either "OK" or "updatingIndex".
When "updatingIndex", this indicates that the index of games is being updated, and the app may experience 
slight delays in its functionality. Things like images, game fetching, and downloads may not be available.

The game index is updated once a month and only take 5-10 minutes to update. It is rare you will experience 
the "updatingIndex" status but if you do you will see a message when searching for games.

## Game Data

### Get Games List
```http
GET /json/games
```
Returns the complete list of games with metadata.

### Get Game Image
```http
GET /v2/image/{imageid}
```
Returns the image for a specific game.

### Get Game Image by Weight
```http
GET /v2/image?place={position}
```
Returns game image based on its weight ranking.

> **Note:** The previous v1 image endpoint has been discontinued due to various issues in the app. The v2 endpoint includes significant improvements such as better timeout handling, enhanced request authentication to better distinguish between Ascendara app requests and external requests, and other optimizations for improved reliability.

## Statistics

### Get Download Statistics
```http
GET /stats/downloads
```
Returns download counts for games.

## Ascendara Information

### Get Current App Version

```http
GET /
```
Returns the current version of Ascendara as "appVer".

API Response:
```json
{
  "appVer":"X.X.X",
  "status":"OK"
}
```

### Get Broken Versions
```http
GET /app/brokenversions
```
Returns a list of known broken versions.
## Changelog

### Get Changelog
```http
GET /json/changelog
```
Returns the changelog information.