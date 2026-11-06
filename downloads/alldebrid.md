# AllDebrid

[AllDebrid](https://alldebrid.com) is a debrid service that converts file-host links into premium direct links and downloads torrents in its cloud. Hosted links need a premium AllDebrid account. See [Debrid Services](/docs/downloads/debrid-services) for settings shared by all services, including the download order.

## Connecting AllDebrid

Open **Settings → Debrid**, turn on **Use debrid services**, and find **AllDebrid** under **Accounts**.

### Sign In with a Code (Recommended)

1. Click **Sign in with a code**.
2. Click **Open the page** to open [alldebrid.com/pin](https://alldebrid.com/pin/), and enter the PIN shown in Ascendara.
3. Approve it on AllDebrid. Ascendara connects as soon as you approve it.

The PIN gives Ascendara an API key for your account, so there's no login to renew. The code expires after about 10 minutes; if it does, click **Sign in with a code** again.

### API Key

1. Click **Get an API key** to open your [AllDebrid API keys](https://alldebrid.com/apikeys/) page.
2. Create a key, copy it, paste it into **API key**, and click **Connect**.

Ascendara checks the key before saving it. To disconnect, click **Disconnect**. You can also delete the key on AllDebrid's API keys page.

Once connected, the card shows your plan, premium end date, and fidelity points.

## Hosted Links

Supported hosted links are unlocked by AllDebrid and then downloaded by Ascendara's normal downloader. Only links from hosts that AllDebrid lists as supported are sent to it. Other links download normally.

Turn on **Prefer over direct downloads** to also send links from seamless providers through AllDebrid.

## Torrents

With **Cloud torrents** on, magnet links are sent to AllDebrid instead of qBittorrent. AllDebrid downloads the torrent in its cloud, and when it's ready, every file continues to your PC and installs like any other download.

While a torrent is in the cloud, it's listed in **Downloads** under **Waiting at your debrid service**. AllDebrid has no page of its own in Ascendara. Ascendara checks on each torrent every 30 seconds.

Turn on **Remove from the service when done** to delete each torrent from your AllDebrid account once its game is on your PC. See [Removing Finished Torrents](/docs/downloads/debrid-services#removing-finished-torrents).

## Troubleshooting

### "AllDebrid could not download this torrent"
AllDebrid gave up on the torrent, for example because it has no seeders or is too large for your account. Remove it from **Downloads** and try another source.

### The code sign-in failed
Make sure you approved the PIN while logged in to the right AllDebrid account, then try again. If it keeps failing, connect with an API key instead.
