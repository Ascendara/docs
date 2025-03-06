# Setting up IGDB for Ascendara

## Introduction

The Internet Game Database (IGDB) integration in Ascendara allows you to access rich metadata for your games, including cover art, descriptions, release dates, and more. Setting up IGDB is a simple process that requires obtaining a Twitch Client ID and Client Secret. If you already have a Twitch account, go to step 2. If you have an application with credentials you can already use, go to step 4.

## Prerequisites

Before you begin, you'll need:
- A Twitch account (IGDB API is managed through Twitch)
- Two-factor authentication enabled on your Twitch account (required for API access)

## Step 1: Create a Twitch Account

If you don't already have a Twitch account:
1. Visit [Twitch.tv](https://www.twitch.tv/)
2. Click on "Sign Up" in the top-right corner
3. Follow the prompts to create your account
4. Enable two-factor authentication in your account settings (required for developer access)

## Step 2: Register a New Application on Twitch

1. Go to the [Twitch Developer Console](https://dev.twitch.tv/console/apps/create)
2. Log in with your Twitch account
3. Click on "Register Your Application"
4. Fill in the required information:
   - **Name**: Choose a name for your application (e.g., "Ascendara IGDB")
   - **OAuth Redirect URLs**: Enter `http://localhost` (this is just a placeholder)
   - **Category**: Select "Application Integration"
5. Click "Create"

## Step 3: Get Your Client ID and Client Secret

After creating your application:
1. You'll be taken to your list of applications
2. Find your newly created application and click "Manage"
3. Note your **Client ID** which is displayed on this page
4. Click "New Secret" to generate a **Client Secret**
5. Copy both the Client ID and Client Secret immediately (the Client Secret will only be shown once)

## Step 4: Add Credentials to Ascendara

1. Go to the **Preferences** tab in the app
2. Scroll down to the **IGDB** card
3. Enter your Client ID in the **Twitch Client ID** field
4. Enter your Client Secret in the **Twitch Client Secret** field
6. Click **Set** to apply your changes

That's it! Ascendara will now be able to fetch game metadata from IGDB to enhance your library and downloads with additional information.

For additional help, refer to the [IGDB API Documentation](https://api-docs.igdb.com/) or join our [discord](https://ascendara.app/discord) for support.