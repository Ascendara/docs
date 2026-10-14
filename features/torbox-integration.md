# TorBox Debrid Service

> **Important:** Ascendara needs a TorBox plan that includes hoster (web) downloads. The free plan isn't enough; use at least the Essential plan.

TorBox is a debrid and seedbox service. A debrid service converts file-host links into premium direct download links, so downloads skip host speed limits, wait times, and CAPTCHAs. TorBox can also download torrents on its servers so you download the finished files directly instead of connecting to peers.

## What TorBox Does in Ascendara

When a TorBox API key is set:

- **Hoster downloads**: Downloads from **1Fichier** and **MegaDB** go through TorBox automatically. Ascendara sends the link to TorBox and downloads the premium direct link it returns.
- **All providers (optional)**: Turn on **Prioritize Torbox over Seamless** to use TorBox for every provider, including GoFile and PixelDrain, whenever TorBox supports the link.
- **Torrents**: With **Use TorBox for torrents** on (the default), magnet links are sent to TorBox instead of being downloaded through qBittorrent. You don't need qBittorrent for torrent downloads when TorBox handles them.
- **TorBox Downloads page**: A **Torbox Downloads** page appears in the navigation bar, showing your TorBox account, plan, downloads in progress, and completed downloads ready to download to your PC.

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

1. Open **Settings** and scroll to the **Download Manager** section.
2. Paste your key into **Torbox API Key**.
3. Click **Set**.

The key is stored encrypted in your Ascendara settings.

## TorBox Settings

These options appear in the **Download Manager** section of **Settings**:

| Setting | Default | What it does |
| --- | --- | --- |
| **Torbox API Key** | Empty | Enables TorBox. |
| **Prioritize Torbox over Seamless** | Off | Uses TorBox even for providers that support seamless downloads. |
| **Use TorBox for torrents** | On | Sends magnet links to TorBox instead of qBittorrent. |
| **Fall back to qBittorrent** | Off | If TorBox can't add a magnet, retries it through your qBittorrent WebUI. This may send torrent traffic outside TorBox, so make sure your VPN is active. |

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
