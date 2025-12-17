# Refreshing Index

The Refreshing Index feature in Ascendara allows you to build and maintain a local game database by scraping SteamRIP directly on your machine. This creates a complete offline-capable index of available games, including cover images and metadata, enabling the fastest possible navigation and browsing experience within Ascendara.

## Overview

When you refresh your index, Ascendara runs a [custom scraping tool](/docs/binary-tool/local-refresh) that:

1. **Fetches all game data** from SteamRIP's database
2. **Downloads cover images** for every game to your local storage
3. **Extracts metadata** including download links, file sizes, versions, and system requirements
4. **Saves everything locally** for instant access without network delays

The result is a dramatically faster browsing experience—games load instantly, images appear without delay, and you can browse your entire game library even when offline.

## Why Use a Local Index?

### Performance Benefits

- **Instant Loading**: Game lists and images load from your local drive instead of fetching from remote servers
- **No Network Latency**: Browse thousands of games without waiting for API responses
- **Offline Access**: View your complete game library even without an internet connection
- **Reduced Bandwidth**: After the initial refresh, browsing uses zero network bandwidth

### When to Refresh

You should refresh your index when:

- Setting up Ascendara for the first time
- New games have been added to SteamRIP that you want to see
- Your local index becomes corrupted or outdated
- You want to ensure you have the latest game metadata and cover images

## Getting Started

### Accessing the Local Refresh Page

1. Open Ascendara
2. Navigate to **Settings** or use the welcome wizard during first-time setup
3. Select **Local Game Index** to access the refresh interface

### Two Ways to Get Your Index

Ascendara offers two methods to obtain a local game index:

#### Option 1: Community Shared Index (Recommended for New Users)

The fastest way to get started is downloading a pre-built index shared by another Ascendara user. This typically takes **less than 20 seconds** to download and extract, giving you immediate access to the complete game library.

#### Option 2: Manual Refresh

Scrape SteamRIP directly from your machine using the [Local Refresh tool](/docs/binary-tool/local-refresh). This takes longer (typically 10-30 minutes depending on your connection) but ensures you have the absolute latest data.

---

## Community Shared Index

The Community Shared Index is a collaborative feature that allows Ascendara users to share their scraped game indexes with the community. This creates a faster onboarding experience for new users and reduces the load on SteamRIP's servers.

### How It Works

When a user completes a manual refresh with eligible settings, their index can be automatically uploaded to Ascendara's cloud storage (Cloudflare R2). Other users can then download this pre-built index instead of scraping SteamRIP themselves.

### Downloading a Shared Index

1. Navigate to the **Local Game Index** page
2. Look for the **Public Index** card at the top (when available)
3. Click **Download** to fetch the latest community-shared index
4. The download includes:
   - Complete game metadata (`ascendara_games.json`)
   - All cover images
   - Category mappings and view counts

The entire process typically completes in under 20 seconds, compared to 10-30 minutes for a manual refresh.

### Index Information

When a shared index is available, you'll see:

- **Game Count**: Total number of games in the index
- **Last Updated**: When the index was last refreshed by a community member
- **Size**: Approximate download size

### Benefits of Using Shared Index

- **Speed**: Get up and running in seconds instead of minutes
- **Simplicity**: No need to deal with Cloudflare cookies or scraping
- **Reliability**: Pre-verified index that's known to work
- **Resource Efficient**: Reduces load on SteamRIP servers

---

## Index Sharing

Index Sharing allows you to contribute your refreshed index back to the Ascendara community. When enabled, your completed index is uploaded to help other users get started quickly.

### Eligibility Requirements

Your index will only be shared if you meet these criteria:

1. **Share Index is enabled** in your Local Refresh settings
2. **Default or no blacklisted games**: You must have either:
   - No custom blacklisted game IDs, OR
   - Only the default blacklisted IDs (`ABSXUc`, `AWBgqf`, `ATaHuq`)

This ensures shared indexes contain the complete game library without personal exclusions that would affect other users.

### Enabling Index Sharing

1. Navigate to the **Local Game Index** page
2. Find the **Share Index** toggle in the action row
3. Click **Enable** to turn on sharing

When sharing is enabled, you'll see an **On** badge with an upload icon.

### What Gets Shared

When you complete a manual refresh with sharing enabled:

- Your `ascendara_games.json` file (game metadata)
- All downloaded cover images
- Category and popularity data

### Privacy Considerations

- **No personal data** is included in shared indexes
- Only game metadata and images are uploaded
- Your blacklist preferences are checked but not transmitted
- Sharing is completely optional and can be disabled at any time

### Blacklist Warning

If you have custom blacklisted games (beyond the defaults), you'll see a warning:

> "Your index won't be shared because you have custom blacklisted games. Remove them to share your index with the community."

This protects other users from receiving incomplete indexes.

---

## Manual Refresh Process

When you choose to refresh manually, Ascendara runs the [Local Refresh tool](/docs/binary-tool/local-refresh) to scrape SteamRIP directly. For technical details about how the scraping works, see the [binary tool documentation](/docs/binary-tool/local-refresh).

### Starting a Manual Refresh

1. Click **Start** on the Manual Refresh card
2. A dialog will appear asking for a **Cloudflare cookie**
3. Follow the instructions to obtain the `cf_clearance` cookie from your browser
4. Paste the cookie and click **Start Refresh**

### Obtaining the Cloudflare Cookie

SteamRIP uses Cloudflare protection, which requires a valid session cookie:

1. Open your web browser and visit [steamrip.com](https://steamrip.com)
2. Complete any Cloudflare challenge that appears
3. Open your browser's Developer Tools (F12)
4. Navigate to **Application** → **Cookies** → `steamrip.com`
5. Find the `cf_clearance` cookie and copy its value
6. Paste this value into Ascendara's refresh dialog

> **Note**: The cookie has a limited validity period. If your refresh takes too long or the cookie expires, you may be prompted to provide a new one.

### Refresh Phases

The manual refresh process goes through several phases (see [detailed phase documentation](/docs/binary-tool/local-refresh#progress-tracking) for more info):

1. **Initializing**: Setting up the scraper and output directories
2. **Fetching Categories**: Loading category mappings from SteamRIP
3. **Fetching Posts**: Downloading all game post data from the API
4. **Processing Posts**: Extracting game metadata, downloading images, and building the index
5. **Saving**: Writing the final output files
6. **Done**: Refresh complete

### Progress Tracking

During the refresh, you'll see:

- **Progress percentage**: Overall completion status
- **Games processed**: Current count vs. total games
- **Current phase**: What the tool is currently doing
- **Errors**: Any issues encountered (expandable list)

### Cookie Expiration Handling

If your Cloudflare cookie expires during the refresh:

1. The progress will pause at "Waiting for cookie"
2. A dialog will automatically appear
3. Obtain a fresh cookie from your browser
4. Submit the new cookie to resume the refresh

The tool maintains your progress, so you won't lose work when refreshing the cookie.

---

## Settings and Configuration

### Storage Location

By default, your local index is stored in Ascendara's application data directory. You can change this location:

1. Click the folder icon next to the storage path
2. Select a new directory
3. The index will be stored in the selected location

### Performance Settings

#### Worker Count (1-16)

Controls how many parallel threads process game data simultaneously. Higher values speed up the refresh but use more system resources.

- **Default**: 8 workers
- **Recommended**: 4-8 for most systems
- **Maximum**: 16 for high-performance systems

#### Games Per Page (10-100)

Controls how many games are fetched per API request during the fetching phase.

- **Default**: 50 games per page
- **Higher values**: Fewer requests, but larger responses
- **Lower values**: More requests, but smaller responses

### Blacklist

The blacklist allows you to exclude specific games from your local index. Games are identified by their 6-character Game ID (see [Game ID encoding](/docs/binary-tool/local-refresh#game-id-encoding) for technical details).

#### Adding Games to Blacklist

1. Enter the Game ID in the blacklist input field
2. Press Enter or click the **+** button
3. The game will be excluded from future refreshes

#### Removing Games from Blacklist

Click the **X** next to any blacklisted Game ID to remove it.

#### Default Blacklisted Games

Ascendara includes three default blacklisted IDs that are pre-configured. These don't affect your ability to share indexes with the community.

---

## Enabling Your Local Index

After refreshing (either via download or manual refresh), you need to enable the local index:

1. Look for the **Enable Index** card
2. If you have a valid index, the card will show "Ready to use"
3. Click **Enable** to switch Ascendara to use your local data

Once enabled, you'll see an **Active** badge next to the page title, and all game browsing will use your local index.

### Switching Back to Online Mode

To disable the local index and return to fetching data from the API:

1. Go to Ascendara's main settings
2. Toggle off the "Use Local Index" option
3. Ascendara will return to fetching game data from remote servers

---

## Output Files

The refresh process creates several files in your index directory. For detailed format specifications, see the [Local Refresh output format documentation](/docs/binary-tool/local-refresh#output-format).

### ascendara_games.json

The main game index file containing:

- Game names, sizes, and versions
- Download links from various providers
- Category classifications
- System requirements
- Cover image references
- Popularity weights (view counts)

### progress.json

Real-time progress tracking file used by the UI to display refresh status. Contains:

- Current phase and status
- Processed/total counts
- Error log
- Timestamps

### imgs/ Directory

Contains all downloaded cover images, named by their unique image IDs. These images are served locally when browsing games, eliminating network requests.

---

## Troubleshooting

### Refresh Fails Immediately

- Ensure your Cloudflare cookie is valid and recently obtained
- Check that SteamRIP is accessible in your browser
- Verify your internet connection is stable

### Refresh Stalls at "Waiting for Cookie"

- Your cookie has expired during the refresh
- Obtain a fresh cookie from your browser and submit it

### Images Not Loading After Refresh

- Clear Ascendara's cache and reload
- Verify the `imgs/` directory contains image files
- Check that the index is properly enabled

### Slow Refresh Performance

- Reduce the worker count if your system is struggling
- Ensure you have sufficient disk space
- Check for network throttling or VPN issues

### Shared Index Download Fails

- Verify the Ascendara API is accessible
- Check your internet connection
- Try again later if the service is temporarily unavailable

---

## Technical Details

For developers and advanced users interested in the underlying implementation, see the [Local Refresh Binary Tool Documentation](/docs/binary-tool/local-refresh) which covers:

- Command-line arguments and usage
- Data extraction algorithms
- Game ID encoding system
- Rate limiting and error handling
- Progress file format specifications
