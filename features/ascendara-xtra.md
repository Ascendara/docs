# Additional Game Information

## What is Ascendara Xtra?
Ascendara Xtra is a feature that enhances your game browsing experience by connecting to external game information providers. It allows you to access rich metadata for your games, including cover art, descriptions, release dates, developer information all when you scroll down to the new card in the games download page or new play-game page.

## Game Information Providers
Xtra supports multiple game information providers that you can configure in the preferences. Each provider offers different types of metadata and coverage for various games. You can only set up one provider to use for game information.

### Available Providers

#### IGDB (Internet Game Database)
IGDB is a structured, developer-backed database with official game details. It provides comprehensive and accurate metadata including cover art, descriptions, release dates, screenshots, developer information, and more. IGDB is maintained by Twitch and offers highly reliable data with regular updates. Setting up IGDB requires a Twitch account and developer credentials.

#### GiantBomb
GiantBomb is a community-driven gaming wiki with in-depth descriptions and extensive historical coverage. It offers rich editorial content, detailed game information, user reviews, ratings, and comprehensive media libraries including screenshots, videos, and concept art. GiantBomb excels at providing context and background information for games, especially for older or more obscure titles. Setting up GiantBomb requires an API key from their developer portal.

### Setting up IGDB

#### Introduction
The Internet Game Database (IGDB) integration in Ascendara allows you to access rich metadata for your games, including cover art, descriptions, release dates, and more. Setting up IGDB is a simple process that requires obtaining a Twitch Client ID and Client Secret. If you already have a Twitch account, go to step 2. If you have an application with credentials you can already use, go to step 4.

#### Prerequisites
Before you begin, you'll need:
- A Twitch account (IGDB API is managed through Twitch)
- Two-factor authentication enabled on your Twitch account (required for API access)

#### Step 1: Create a Twitch Account
If you don't already have a Twitch account:
1. Visit [Twitch.tv](https://www.twitch.tv/)
2. Click on "Sign Up" in the top-right corner
3. Follow the prompts to create your account
4. Enable two-factor authentication in your account settings (required for developer access)

#### Step 2: Register a New Application on Twitch
1. Go to the [Twitch Developer Console](https://dev.twitch.tv/console/apps/create)
2. Log in with your Twitch account
3. Click on "Register Your Application"
4. Fill in the required information:
   - **Name**: Choose a name for your application (e.g., "Ascendara IGDB")
   - **OAuth Redirect URLs**: Enter `http://localhost` (this is just a placeholder)
   - **Category**: Select "Application Integration"
5. Click "Create"

#### Step 3: Get Your Client ID and Client Secret
After creating your application:
1. You'll be taken to your list of applications
2. Find your newly created application and click "Manage"
3. Note your **Client ID** which is displayed on this page
4. Click "New Secret" to generate a **Client Secret**
5. Copy both the Client ID and Client Secret immediately (the Client Secret will only be shown once)

#### Step 4: Add Credentials to Ascendara
1. Go to the **Preferences** tab in the app
2. Scroll down to **Ascendara Xtra**
3. Select **IGDB**
4. Paste your Client ID in the **Twitch Client ID** field
5. Paste your Client Secret in the **Twitch Client Secret** field
6. Click **Set** to apply your changes

### Setting up GiantBomb

To use GiantBomb with Ascendara:

1. Visit [GiantBomb's website](https://www.giantbomb.com/) and create an account
2. Go to the [API page](https://www.giantbomb.com/api/) and get your API key
3. Go to the **Preferences** tab in the app
4. Scroll down to **Ascendara Xtra**
5. Select **GiantBomb**
6. Paste your API key in the **GiantBomb API Key** field
7. Click **Set** to apply your changes

That's it! You can now access GiantBomb's video game database through Ascendara.

## Benefits of Using Ascendara Xtra
- Enhanced game metadata in your library
- Detailed game information including genres, platforms, and release dates
- High-quality cover art and screenshots
- More accurate game identification during downloads
- Improved organization and filtering options

For additional help with specific providers, refer to their documentation or join our [Discord community](https://ascendara.app/discord) for support.