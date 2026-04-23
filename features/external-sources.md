# External Sources and Game Index

Ascendara uses a game index system to manage game data locally on your computer. This allows for fast browsing, reduced network usage, and offline access when using Ascendara's official index.

## Overview

The game index acts as a local database containing:

- Game listings and metadata
- Download links and source references
- Cover images
- Categories, ratings, and popularity data when available

By storing this data locally, Ascendara can provide a fast browsing experience without relying on repeated network requests.

---

## Choosing a Game Source

Ascendara supports two source modes. Each offers a different balance of performance, features, and flexibility.

### Official Ascendara Index (Recommended)

The official index provides the full Ascendara experience and is the default option.

**Features**

- Full metadata support
- Categories, ratings, and popularity data
- Locally cached data and images
- Fast browsing and offline-friendly behavior
- Full Ascendara feature compatibility

**Storage Requirement**
Approximately 200 MB

**Best for**
Users who want the fastest experience and full feature support.

### External Sources Mode

External Sources Mode allows Ascendara to load games from an external source bucket URL instead of the official Ascendara index.

This mode is designed for flexibility and custom sourcing. Users can switch to a supported external bucket and have Ascendara build the game list from that source.

**Features**

- Supports external bucket URLs
- Lets users choose their own supported source
- Lower local storage usage
- Flexible for custom or community-maintained sources

**Limitations**

- Reduced metadata support
- No categories, ratings, or popularity data
- Browsing by category is unavailable
- Sorting by popularity is unavailable
- Cover images are not provided by the source bucket itself
- Some visual and metadata-based Ascendara features may be limited

**Best for**
Users who want a lightweight or customizable source option.

---

## Source Bucket URLs

When External Sources Mode is enabled, Ascendara reads from a source bucket URL that points to a supported source collection.

Users can paste a supported bucket URL into the Source Bucket URL field in Ascendara preferences. Once set, Ascendara will load games from that source.

### How It Works

- The source bucket URL points to a supported game source collection
- Ascendara reads the collection and converts it into a usable game list
- The loaded source is then shown under your available sources
- Users can switch between available sources while External Sources Mode is enabled

### Notes

- The source must be publicly reachable by Ascendara
- The source must follow one of Ascendara's supported formats
- Metadata availability depends on the source format and data provided
- External sources do not provide the same completeness as the official Ascendara index

---

## Reduced Metadata in External Sources Mode

When using external sources, Ascendara may not have access to the full metadata available in the official index.

This means the following features may be unavailable for games loaded from external sources:

- Cover images provided directly by the index
- Categories
- Ratings
- Popularity data
- Popularity-based sorting
- Category-based browsing

Ascendara may still attempt to fill in certain visual data dynamically when possible, but this is not guaranteed and depends on the source data and title matching.

---

## Supported Source Formats

Ascendara supports multiple source structures for external source buckets.

### 1. Ascendara JSON Format (Full Format)

This is the native Ascendara format and provides the highest level of compatibility.

```json
{
  "games": [
    {
      "category": ["Action", "Adventure", "Indie"],
      "dirlink": "https://example.com/game-download/",
      "dlc": false,
      "download_links": {},
      "game": "Sample Game",
      "gameID": "AbCdEf",
      "imgID": "sample123",
      "latest_update": "2026-04-21",
      "minReqs": {
        "cpu": "Intel Core i5 or equivalent",
        "directx": "Version 11",
        "gpu": "NVIDIA GeForce GTX 1060 or equivalent",
        "os": "Windows 10 64-bit",
        "ram": "8 GB RAM",
        "storage": "10 GB available space"
      },
      "online": false,
      "releasedBy": "Sample Group",
      "size": "15.0 GB",
      "version": "1.0.0",
      "weight": "5000"
    }
  ]
}
```

**Supported fields include**

- `game` — game title
- `gameID` — unique identifier
- `imgID` — image reference used by Ascendara's official index format
- `dirlink` — source or page link
- `download_links` — structured download links
- `version` — version information
- `latest_update` — update tracking
- `size` — game size
- `category` — categories or genres
- `weight` — ranking or weight value
- `releasedBy` — source attribution
- `dlc` — whether the entry is DLC
- `online` — whether online support is noted
- `minReqs` — minimum system requirements

> **Important**  
> In External Sources Mode, not all fields are used equally. Some metadata may be ignored depending on the source behavior and the current Ascendara implementation.

### 2. Simplified Download Collection Format

Ascendara also supports a simplified collection format focused mainly on titles, file sizes, links, and upload metadata.

```json
{
  "name": "SampleCollection",
  "downloads": [
    {
      "title": "Sample Game Free Download (v1.0.0)",
      "fileSize": "500 MB",
      "uris": [
        "https://example.com/download1",
        "https://example.com/download2"
      ],
      "uploadDate": "2025-03-05T16:41:20.000Z"
    }
  ]
}
```

**Supports**

- Game titles
- File sizes
- Download links
- Upload dates
- Collection naming

This format is useful for lightweight source buckets where only core download data is available.

### Format Behavior

Different formats provide different levels of functionality inside Ascendara.

**Ascendara JSON Format**
- Best compatibility
- Supports richer metadata
- Better alignment with Ascendara's internal systems
- Preferred when building a complete custom source

**Simplified Download Collection Format**
- Lightweight
- Easy to host and maintain
- Focused on download data
- May result in reduced metadata and fewer supported features inside Ascendara

---

## Image Handling

**Official Index**
- Images are handled through Ascendara's official index data
- Optimized for consistency and speed

**External Sources**
- Images are not guaranteed as part of the source bucket
- Visual results may be limited or resolved dynamically when possible
- Image quality and consistency may vary depending on title matching and available data

Because of this, external sources should be expected to provide a more limited browsing experience than the official index.

---

## Performance Considerations

### Official Index
- Fastest browsing experience
- Better caching behavior
- More complete metadata
- Better support for Ascendara features
- Higher storage usage

### External Sources
- Lower storage usage
- More flexible source selection
- May load more slowly depending on the source
- Limited metadata support
- More dependent on source structure and availability

---

## Switching Between Source Modes

You can switch between the official index and external sources at any time.

1. Open Ascendara Preferences
2. Go to the Game Index or External Sources section
3. Enable or disable External Sources Mode
4. If enabled, paste a supported Source Bucket URL
5. Select the source you want Ascendara to use
6. Ascendara will reload the game list using the selected source

---

## Troubleshooting

### External source does not load
- Make sure the bucket URL is correct
- Confirm the source is publicly reachable
- Verify that the source uses a supported format
- Try reloading the source after saving the URL

### Games are missing metadata
- External sources may not provide full metadata
- Categories, ratings, popularity data, and cover images may not be available
- For full metadata support, use the official Ascendara index

### Images are missing or inconsistent
- External sources may not include image data
- Dynamically resolved visuals may vary based on title matching
- This is expected behavior in External Sources Mode

### Browsing feels more limited
- External sources do not support the full official-index experience
- For full filtering, popularity sorting, and metadata-driven features, switch back to the official Ascendara index

---

## Summary

- **The Official Ascendara Index** provides the full Ascendara experience with better performance and full metadata support
- **External Sources Mode** allows users to load games from supported source bucket URLs
- External sources are more flexible, but come with reduced metadata and fewer browsing features
- For the best overall experience, the official Ascendara index is recommended