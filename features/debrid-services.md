# Debrid Services

A debrid service converts file-host links into premium direct download links, so downloads skip host speed limits, wait times, and CAPTCHAs. Most debrid services can also download torrents on their servers, so you download the finished files directly instead of connecting to peers.

Ascendara supports two debrid services:

- [TorBox](/docs/features/torbox-integration)
- [Real-Debrid](/docs/features/real-debrid)

You can connect one or both.

## Turning Debrid On

Debrid services are off by default.

1. Open **Settings → Downloads**.
2. Turn on **Debrid services**.
3. Select a service, paste its API key, and save it.

Ascendara stores API keys encrypted with your other credentials. To disconnect a service, clear its key and save.

Turning **Debrid services** off keeps your API keys and priority order, and pauses any queued debrid downloads.

## Service Priority

If you connect more than one service, drag them in **Service priority** (or use the arrow buttons) to choose their order. TorBox is first unless you change the order.

- **Hosted links** use the first connected, enabled service that supports the link.
- **Magnet links** use the first service with torrents enabled.

If a service returns an error, Ascendara doesn't automatically retry the download with another service.

## Supported Hosts

Each service publishes a list of the file hosts it supports. Ascendara only sends a link to a service if the link's hostname (including aliases and subdomains) is on that service's list. Links from unsupported hosts download normally.

- The host lists are cached for 24 hours.
- If a list can't be refreshed, Ascendara keeps using the last valid list and tries again after five minutes.
- Without a valid list, hosted links aren't sent to that service.

A supported host can still be temporarily unavailable on the service. In that case the download fails with an error from the service; choose another provider for the game.

## Seamless Providers

By default, providers that support [seamless downloads](/docs/features/downloading) keep using them. Turn on the service's **Prefer over seamless** option (for example **Prioritize Torbox over Seamless**) to send those links through the debrid service as well, as long as the service supports them.

## Torrents

With a service's torrent option on, magnet links are sent to that service's cloud instead of qBittorrent, so you don't need qBittorrent for those downloads.

When a torrent submitted through Ascendara finishes in the cloud, it automatically continues to your PC:

- It downloads to the library folder you selected when you started the download.
- It waits while another local download, extraction, or verification is running.
- Ascendara's downloader extracts and verifies the files as usual. On Windows, repacks with a setup installer show the **Run installer as Administrator** prompt in **Downloads**.
- Torrents that were already handed off aren't downloaded again, and existing game folders aren't overwritten.

Torrents you add to your debrid account outside Ascendara aren't downloaded automatically. Download them manually from the service's downloads page.
