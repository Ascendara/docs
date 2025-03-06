# Error Handling

## Error Responses
API errors are returned with appropriate HTTP status codes and error messages in JSON format:

```json
{
  "error": "error_message"
}
```

## Common Status Codes
- 200: Success
- 400: Bad Request
- 404: Not Found
- 429: Rate Limit Exceeded
- 500: Internal Server Error

## Error Messages
Common error messages include:
- "invalid function call" (404)
- "server error" (500)
- "Rate limit exceeded" (429)

## Best Practices
1. Always check response status codes
2. Handle rate limiting appropriately
3. Implement proper error handling
4. Log error responses for debugging