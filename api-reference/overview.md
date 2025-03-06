# Ascendara API Overview

## Introduction
The Ascendara API is a custom-developed RESTful interface that powers the Ascendara game discovery application. It provides access to game information, download statistics, system health data, and image assets. This API is designed to be lightweight, efficient, and focuses on managing game testing listings without storing any actual game files.

## API Basics

### Base URL
All API requests should be made to:
```
https://api.ascendara.app
```

### Response Format
All responses are returned in JSON format. The structure varies by endpoint type:

#### Success Responses
Health check:
```json
{
  "status": "healthy",
  "version": "5.5",
  "endpoint": "ascendara-api"
}
```

Download tracking:
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

#### Error Responses
Error responses follow a consistent format:
```json
{
  "error": "error_message"
}
```

### Rate Limiting
The API implements rate limiting to ensure fair usage and system stability:
- Image endpoints: 30 requests per 60 seconds
- General endpoints: 3 requests per hour
- Rate limits are tracked per IP address
- Custom rate limits may apply for specific endpoints

## Core Features

### Game Discovery
- List all available games with metadata
- Sort games by popularity weight
- Access game preview images
- Track download statistics

### System Integration
- Health check endpoint for system status
- Version tracking and changelog access
- Download statistics and metrics

### Image Service
- Secure image delivery system
- Support for multiple image formats (PNG, JPG, JPEG)
- Rate-limited access to prevent abuse

### Error Handling
All endpoints follow a consistent error reporting format:
```json
{
  "error": "error_message",
  "status": error_code
}
```

Common HTTP status codes:
- 200: Success
- 400: Bad Request
- 404: Not Found
- 429: Rate Limit Exceeded
- 500: Server Error