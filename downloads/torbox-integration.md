# TorBox Debrid Service

> **Important:** Ascendara needs a TorBox plan that includes hoster (web) downloads. The free plan isn't enough; use at least the Essential plan.

TorBox is a debrid and seedbox service. A debrid service converts file-host links into premium direct download links, so downloads skip host speed limits, wait times, and CAPTCHAs. TorBox can also download torrents on its servers so you download the finished files directly instead of connecting to peers.

See [Debrid Services](/docs/downloads/debrid-services) for settings shared by all services, including the download order.

## What TorBox Does in Ascendara

When TorBox is connected:

- **Hoster downloads**: Links from hosts that TorBox supports go through TorBox automatically. TorBox processes the link in its cloud first, even if a local download is already running, and you then download it to your PC from the **Debrid Downloads** page.
- **Magic Link hosts (optional)**: GoFile, PixelDrain, and BuzzHeavier keep using [Magic Links](/docs/features/magic-links) by default. Turn on **Prefer over direct downloads** to use TorBox for them too, whenever TorBox supports the link.
- **Torrents**: With **Cloud torrents** on (the default), magnet links are sent to TorBox instead of being downloaded through qBittorrent. You don't need qBittorrent for torrent downloads when TorBox handles them. When a torrent finishes in the cloud, it continues to your PC automatically and is extracted like any other download.
- **Debrid Downloads page**: A **Debrid Downloads** page appears in the navigation bar. Choose **TorBox** to see your account, plan, downloads in progress, and completed downloads ready to download to your PC.

## Supported Hosts

Ascendara checks links against the hosts and aliases listed by TorBox's public [`/webdl/hosters` endpoint](https://api-docs.torbox.app/). Exact hostnames and their subdomains match. Links from unsupported hosts download normally.

The list is cached for 24 hours. If it can't be refreshed, Ascendara keeps the last valid list and tries again after five minutes. A host that TorBox supports can still be temporarily unavailable; TorBox reports that as an error when you start the download.

On a game's download page, you can disable TorBox for a single download, for example if TorBox doesn't support that provider's link.

## TorBox Plans

TorBox offers several plans (Free, Essential, Standard, and Pro). For current features, limits, and prices, see the [TorBox pricing page](https://torbox.app/pricing). Plans have limits on download size and on the number of concurrent downloads.

## How to Get Your TorBox API Key

### Step 1: Create a TorBox Account

1. Visit the [TorBox website](https://torbox.app).
2. Sign up and verify your email address.

### Step 2: Subscribe to a Compatible Plan

1. Log in to your TorBox account.
2. Open the [pricing page](https://torbox.app/pricing).
3. Choose the Essential plan or higher and complete the payment.

### Step 3: Generate Your API Key

1. In your TorBox account, open your settings.
2. Find the **API** section.
3. Generate an API key and copy it.

### Step 4: Add the Key to Ascendara

1. Open **Settings → Debrid** and turn on **Use debrid services**.
2. Find **TorBox** under **Accounts** and paste your key into **API key**.
3. Click **Connect**. Ascendara checks the key before saving it.

The key is stored in your operating system's keychain with your other saved credentials.

## TorBox Settings

These options appear on the **TorBox** card in **Settings → Debrid**:

| Setting | Default | What it does |
| --- | --- | --- |
| **API key** | Empty | Connects TorBox. |
| **Prefer over direct downloads** | Off | Uses TorBox even for Magic Link hosts. |
| **Cloud torrents** | On | Sends magnet links to TorBox instead of qBittorrent. |
| **Fall back to qBittorrent** | Off | If TorBox can't add a magnet, retries it through your qBittorrent WebUI. Requires local torrenting to be on. This may send torrent traffic outside TorBox, so make sure your VPN is active. |

## Troubleshooting TorBox

### API Key Not Working
- Make sure your plan includes hoster downloads.
- Regenerate the API key in TorBox and set it again in Ascendara.
- Check that no spaces or extra characters were copied with the key.

### "This provider is offline on Torbox"
TorBox can't currently download from that provider. Choose another provider for the game.

### "TorBox does not support this download link"
Choose another provider, or disable TorBox for this download on the game's download page.

### Connection Issues
- Verify your internet connection.
- Check whether TorBox is experiencing downtime.
- Make sure your firewall isn't blocking the connection.
- Try enabling a VPN.

## Acknowledgement

A special thank you to Wamy, the owner of TorBox, for making this integration possible and for working closely with Ascendara to give its users the best experience.
