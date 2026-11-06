# Debrid Services

A debrid service converts file-host links into premium direct download links, so downloads skip host speed limits, wait times, and CAPTCHAs. Debrid services can also download torrents on their servers, so you download the finished files directly instead of connecting to peers.

Ascendara supports five debrid services:

| Service | Sign in | Hosted links | Cloud torrents shown in |
| --- | --- | --- | --- |
| [TorBox](/docs/downloads/torbox-integration) | API key | Processed in the TorBox cloud first | **Debrid Downloads** |
| [Real-Debrid](/docs/downloads/real-debrid) | Code or API key | Direct | **Debrid Downloads** |
| [AllDebrid](/docs/downloads/alldebrid) | Code (PIN) or API key | Direct | **Downloads** |
| [Premiumize](/docs/downloads/premiumize) | API key | Direct | **Downloads** |
| [Debrid-Link](/docs/downloads/debrid-link) | API key | Direct | **Downloads** |

You can connect any number of them. Each one needs its own account, and most need a paid plan for hosted links.

## Turning Debrid On

Debrid services are off by default.

1. Open **Settings → Debrid**.
2. Turn on **Use debrid services**.
3. Under **Accounts**, connect each service you have an account with.

Once debrid is on, the top of the page shows how many services are connected and where **Hosted links** and **Magnets** go right now.

Turning **Use debrid services** off keeps your accounts and download order, and pauses any queued debrid downloads.

## Connecting an Account

Each service has a card under **Accounts**. To connect one:

- **Sign in with a code** (Real-Debrid and AllDebrid): click **Sign in with a code**, then **Open the page**, and approve the code shown in Ascendara on the service's website. Ascendara connects as soon as you approve it.
- **API key** (all services): click **Get an API key** to open the service's key page, copy the key, paste it into **API key**, and click **Connect**.

Ascendara checks the key with the service before saving it. Keys are stored in your operating system's keychain with your other saved credentials.

Once connected, the card shows your account name, plan, premium end date, and account stats such as points or fair use. Use **Change key** to replace the key, or **Disconnect** to remove it.

### Pausing a Service

The switch in the top-right corner of each card turns that service on or off without disconnecting it. A paused service keeps its key and options but isn't used for new or queued downloads. Queued downloads that were meant for it go to another connected service that supports the link.

## Service Options

Each card has its own **Download options**. They only apply to that service.

| Option | Default | What it does |
| --- | --- | --- |
| **Cloud torrents** | On | Lets this service handle magnet links instead of qBittorrent. |
| **Prefer over direct downloads** | Off | Also uses this service for hosts that Ascendara can already [download seamlessly](/docs/downloads/downloading#seamless-downloads). |
| **Remove from the service when done** | Off | AllDebrid, Premiumize, and Debrid-Link only. Deletes a torrent from your account once its game is on your PC. |
| **Fall back to qBittorrent** | Off | TorBox only. Retries a magnet through qBittorrent if TorBox can't add it. Needs local torrenting on. |

## Download Order

If you connect more than one service, drag them in **Download order** (or use the arrow buttons) to choose the order they're tried in. TorBox is first unless you change the order.

- **Hosted links** go to the first connected, enabled service that supports the link's host.
- **Magnet links** go to the first connected, enabled service with **Cloud torrents** on.

If a service returns an error, Ascendara doesn't automatically retry the download with another service. Choose another provider for the game, or move a different service to the top.

## Supported Hosts

Each service publishes a list of the file hosts it supports. Ascendara only sends a link to a service if the link's hostname (including aliases and subdomains) is on that service's list. Links from unsupported hosts download normally.

- The host lists are cached for 24 hours (15 minutes for Debrid-Link, which also reports hosts that are offline).
- If a list can't be refreshed, Ascendara keeps using the last valid list and tries again after five minutes.
- Without a valid list, hosted links aren't sent to that service.

A supported host can still be temporarily unavailable on the service. In that case the download fails with an error from the service; choose another provider for the game.

On a game's download page, you can turn debrid off for a single download, for example if the service doesn't support that provider's link.

## Seamless Providers

By default, providers that support [seamless downloads](/docs/downloads/downloading#seamless-downloads) keep using them. Turn on a service's **Prefer over direct downloads** option to send those links through the debrid service as well, as long as the service supports them.

## Torrents

With a service's **Cloud torrents** option on, magnet links are sent to that service's cloud instead of qBittorrent, so you don't need qBittorrent for those downloads.

When a torrent submitted through Ascendara finishes in the cloud, it automatically continues to your PC:

- It downloads to the library folder you selected when you started the download.
- It waits while another local download, extraction, or verification is running.
- Ascendara's downloader extracts and verifies the files as usual. On Windows, repacks with a setup installer show the **Run installer as Administrator** prompt in **Downloads**.
- Torrents that were already handed off aren't downloaded again, and existing game folders aren't overwritten.

Torrents you add to your debrid account outside Ascendara aren't downloaded automatically.

### Following Cloud Torrents

- **TorBox and Real-Debrid** torrents appear on the **Debrid Downloads** page in the navigation bar.
- **AllDebrid, Premiumize, and Debrid-Link** torrents appear in **Downloads** under **Waiting at your debrid service** until they reach your PC. Each one shows whether it's queued, downloading in the cloud, or ready. Click **Remove** to delete a torrent from your account and cancel it.

### Failed and Stalled Torrents

- If the service gives up on a torrent, Ascendara shows the service's error once and marks the torrent **Failed**. Remove it and try another source.
- If a torrent makes no progress in the cloud for 3 days, Ascendara marks it **Stalled** and tells you. This usually means the torrent has no seeders. A stalled torrent that starts moving again continues normally.

### Removing Finished Torrents

Most services limit how many torrents an account can hold at once. For AllDebrid, Premiumize, and Debrid-Link, turn on **Remove from the service when done** to delete each torrent from your account once its game is fully on your PC. This also applies to games you downloaded before turning it on.

A torrent is kept while its game is still downloading, stopped, or failed, and is kept for good if you deleted the game before it finished.
