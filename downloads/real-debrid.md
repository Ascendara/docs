# Real-Debrid

[Real-Debrid](https://real-debrid.com) is a debrid service that converts file-host links into premium direct links and downloads torrents in its cloud. See [Debrid Services](/docs/downloads/debrid-services) for settings shared by all services, including the download order.

## Connecting Real-Debrid

Open **Settings → Debrid**, turn on **Use debrid services**, and find **Real-Debrid** under **Accounts**.

### Sign In with a Code (Recommended)

1. Click **Sign in with a code**.
2. Click **Open the page** to open [real-debrid.com/device](https://real-debrid.com/device), and enter the code shown in Ascendara.
3. Approve Ascendara on Real-Debrid. Ascendara connects as soon as you approve it.

A code sign-in renews itself, so you don't need to sign in again. If you revoke Ascendara's access on Real-Debrid, sign in again.

### API Key

1. Click **Get an API key** to open your [Real-Debrid API token](https://real-debrid.com/apitoken) page.
2. Copy the key, paste it into **API key**, and click **Connect**.

Ascendara checks the key before saving it. Saving a new key ends a code sign-in. To disconnect, click **Disconnect**.

Once connected, the card shows your plan, premium end date, points, and remaining premium days.

## Hosted Links

Supported hosted links are unrestricted by Real-Debrid and then downloaded by Ascendara's normal downloader. Only links whose hostname is on Real-Debrid's supported domain list (including aliases and subdomains) are sent to Real-Debrid. Other links download normally.

Turn on **Prefer over direct downloads** to also send links from seamless providers through Real-Debrid.

Queued links are checked again when the queue starts them, using your current API key.

## Torrents

With **Cloud torrents** on, magnet links are sent to Real-Debrid instead of qBittorrent.

Ascendara selects all of the torrent's files and starts the cloud download as soon as Real-Debrid has the torrent's metadata. This keeps running in the background when you leave the downloads page, and pending torrents resume after you restart Ascendara.

When the cloud download finishes, all files continue to your PC together, with combined progress and pause/resume support. See [Debrid Services](/docs/downloads/debrid-services#torrents) for how the handoff works.

## Debrid Downloads

The **Debrid Downloads** page (choose **Real-Debrid** if you use more than one service) shows cloud progress and your download history, split into **Ready**, **In cloud**, and **Sent to PC**.

- **View download** opens the Downloads page once a torrent has been handed off to your PC.
- For torrents added outside Ascendara, choose their files and click **Download to PC** to download them manually.
