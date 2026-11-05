# Real-Debrid

[Real-Debrid](https://real-debrid.com) is a debrid service that converts file-host links into premium direct links and downloads torrents in its cloud. See [Debrid Services](/docs/features/debrid-services) for how Ascendara chooses between services.

## Connecting Real-Debrid

1. Open **Settings → Downloads** and turn on **Debrid services**.
2. Select **Real-Debrid** and click **Get your API key** to open your [Real-Debrid API token](https://real-debrid.com/apitoken) page.
3. Copy the key, paste it into **Real-Debrid API key**, and save it.

Ascendara checks the key before saving it. To disconnect, clear the key and save.

## Hosted Links

Supported hosted links are unrestricted by Real-Debrid and then downloaded by Ascendara's normal downloader. Only links whose hostname is on Real-Debrid's supported domain list (including aliases and subdomains) are sent to Real-Debrid. Other links download normally.

Turn on **Prefer Real-Debrid over seamless downloads** to also send links from seamless providers through Real-Debrid.

Queued links are checked again when the queue starts them, using your current API key.

## Torrents

With **Use Real-Debrid for torrents** on, magnet links are sent to Real-Debrid instead of qBittorrent.

Ascendara selects all of the torrent's files and starts the cloud download as soon as Real-Debrid has the torrent's metadata. This keeps running in the background when you leave the downloads page, and pending torrents resume after you restart Ascendara.

When the cloud download finishes, all files continue to your PC together, with combined progress and pause/resume support. See [Debrid Services](/docs/features/debrid-services#torrents) for how the handoff works.

## Real-Debrid Downloads

The **Real-Debrid Downloads** page shows cloud progress and your download history.

- **View local download / installation** opens the Downloads page once a torrent has been handed off to your PC.
- For torrents added outside Ascendara, use **Show files** and **Download to PC** to choose and download files manually.
