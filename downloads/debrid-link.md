# Debrid-Link

[Debrid-Link](https://debrid-link.com) is a debrid service that converts file-host links into premium direct links and downloads torrents in its cloud. Hosted links need a premium Debrid-Link account. See [Debrid Services](/docs/downloads/debrid-services) for settings shared by all services, including the download order.

## Connecting Debrid-Link

1. Open **Settings → Debrid**, turn on **Use debrid services**, and find **Debrid-Link** under **Accounts**.
2. Click **Get an API key** to open your [Debrid-Link API key](https://debrid-link.com/webapp/apikey) page, and copy the key.
3. Paste it into **API key** and click **Connect**.

Ascendara checks the key before saving it. To disconnect, click **Disconnect**.

Once connected, the card shows your plan, premium end date (or **Lifetime premium**), and points.

## Hosted Links

Supported hosted links are converted to direct links by Debrid-Link and then downloaded by Ascendara's normal downloader. Only links from hosts that Debrid-Link lists as supported are sent to it. Other links download normally.

Debrid-Link also reports which hosts are currently offline. Ascendara leaves offline hosts out, so their links go to the next service in your **Download order**, or download normally. Because of this, Debrid-Link's host list is refreshed every 15 minutes instead of every 24 hours.

Turn on **Prefer over direct downloads** to also send links from [Magic Link](/docs/features/magic-links) hosts through Debrid-Link.

## Torrents

With **Cloud torrents** on, magnet links are added to your Debrid-Link seedbox instead of qBittorrent. When every file in the torrent reaches 100%, the files continue to your PC and install like any other download.

While a torrent is in the cloud, it's listed in **Downloads** under **Waiting at your debrid service**. Debrid-Link has no page of its own in Ascendara. Ascendara checks on each torrent every 30 seconds.

Debrid-Link doesn't report failed torrents. A torrent that makes no progress for 3 days is marked **Stalled**; see [Failed and Stalled Torrents](/docs/downloads/debrid-services#failed-and-stalled-torrents).

Turn on **Remove from the service when done** to delete each torrent from Debrid-Link once its game is on your PC. See [Removing Finished Torrents](/docs/downloads/debrid-services#removing-finished-torrents).
