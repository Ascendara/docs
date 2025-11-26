# API Endpoints Reference

---

## Health & Status

### Get Health Status
**GET `/health`**

Returns API health, version, and index status.

**Response:**
```json
{
  "status": "healthy",
  "version": "5.5",
  "endpoint": "ascendara-api"
}
```
- `status`: "healthy" (normal) or "updatingIndex"
- `version`: API version
- `endpoint`: Service name

**Rate Limit:** 3/hour per IP

### About Ascendara Status

This endpoint also returns the status of Ascendara's index, which can be either "healthy" or "updatingIndex".
When "updatingIndex", this indicates that the index of games is being updated, and the app may experience 
slight delays in its functionality. Things like images, game fetching, and downloads may not be available.


The game index is updated once a month and only take 5-10 minutes to update. It is rare you will experience 
the "updatingIndex" status but if you do you will see a message when searching for games.


### Get Current App Version
**GET `/`**

Returns the running version of Ascendara.

**Response:**
```json
{
  "appVer": "X.X.X",
  "status": "OK",
  "readme": "...",
  "documentation": "..."
}
```

---

## Game Data

### Get Games List
**GET `/json/games`**

Returns a list of all available games with metadata.

See [Game List Endpoint Schema](https://ascendara.app/docs/api-reference/games-list#game-object-keys) for full response details, object keys, and examples.

---

### Get Game Image
**GET `/v2/image/{imageid}`**

Returns the image file for a game by its imageid.

**Path Parameter:**
- `imageid` (string): The image ID from the game metadata

**Response:**
- Image file (PNG/JPG/JPEG)
- Error key if not found

**Rate Limit:** 30/min per IP

---

### Get Game Image by Weight
**GET `/v2/image?place={position}`**

Returns the image for the game ranked at the given weight position.

**Query Parameter:**
- `place` (integer, required): 1-based ranking

**Response:**
- Image file (PNG/JPG/JPEG)
- Error key if invalid place, not found

**Rate Limit:** 30/min per IP

---

## Statistics

### Get Download Statistics
**GET `/stats/downloads`**

Returns download counts for all games and total.

**Response:**
```json
{
  "total": 123,
  "Game Name 1": 12,
  ...
}
```

---

### Track Download
**POST `/stats/download`**

Increments the download count for a game.

**Body:**
```json
{
  "game": "Game Name"
}
```

**Response:**
```json
{
  "message": "Download tracked successfully"
}
```

---

## Information

### Get Changelog
**GET `/json/changelog`**

Returns the changelog information.

**Response:**
```json
{
  ... // Changelog content
}
```

---

### Get Roadmap
**GET `/json/roadmap`**

Returns the roadmap information.

**Response:**
```json
{
  ... // Roadmap content
}
```

---

### Get Language Version
**GET `/language/version`**

Returns the current language file version.

**Response:**
```json
{
  "version": "1.0.0"
}
```

---

### Get Language Translation
**GET `/language/en`**

Returns the English translation file.

**Response:**
```json
{
  ... // Translation key-value pairs
}
```

---

### Get VirusTotal Results
**GET `/virustotal/latest`**

Returns the latest VirusTotal scan results.

**Response:**
```json
{
  ... // VirusTotal data
}
```

---

### Get Average App Rating
**GET `/average-app-rating`**

Returns the average app rating and total count.

**Response:**
```json
{
  "average_rating": 4.8,
  "total_count": 1234
}
```

---

### Get OpenCritic Game Info
**GET `/opencritic/game?name={game}`**

Returns OpenCritic and HowLongToBeat info for a game.

**Query Parameter:**
- `name` (string, required): Game name

**Response:**
```json
{
  "name": "Game Name",
  "medianScore": 85,
  ... // see API for all fields
}
```

---

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