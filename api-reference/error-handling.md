# Error Handling

When a request fails, the API returns an HTTP error status with a JSON body. Check the status code first; the message is meant for logs and people, not for parsing.

## Error Format

Most errors look like this:

```json
{ "error": "Game not found" }
```

Endpoints that report a `success` field, such as the `/localindex/*` endpoints, include it in errors too:

```json
{ "success": false, "error": "No index metadata available" }
```

## Status Codes

| Code | Meaning | Example `error` |
| --- | --- | --- |
| `200` | Success | |
| `304` | Not modified (when you send `If-None-Match` and nothing changed) | No body |
| `400` | The request is missing or has an invalid parameter | `"games must be an array"`, `"Invalid place parameter"` |
| `404` | The game, image, or endpoint doesn't exist | `"Game not found"`, `"Image not found"`, `"invalid function call"` (unknown path) |
| `405` | Wrong HTTP method for the endpoint | `"Method Not Allowed"` |
| `413` | Too many items in a batch request | `"Too many games in one request (max 200)"` |
| `429` | Rate limit exceeded | See below |
| `500` | Something went wrong on the server | `"Internal server error"`, `"server error"` |
| `503` | Temporarily unavailable, for example while the version history is being rebuilt | Retry later |

## Rate Limits

Limits are per IP address. When you go over, you get `429`:

```json
{ "success": false, "error": "60 per 1 minute" }
```

Image endpoints use a separate limit (30 per minute) and respond with:

```json
{ "error": "rate limit exceeded" }
```

Wait before retrying, and back off further if you keep getting `429`. If your client keeps sending requests after being limited, its IP can be blocked:

```json
{ "error": "perm_rate_limit", "detail": "You have been rate limited because of unusual activity. ..." }
```

## Tips

- Treat any `5xx` as temporary and retry with a growing delay.
- Cache the game list and send `If-None-Match`, so unchanged lists come back as a `304` with no body.
- A `404` from `/v3/game/...` means the game isn't in the current index. It may have been removed or renamed.
