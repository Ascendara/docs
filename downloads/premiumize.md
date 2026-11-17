# Premiumize

[Premiumize](https://www.premiumize.me) is a debrid and cloud storage service that converts file-host links into premium direct links and downloads torrents in its cloud. Ascendara needs a premium Premiumize account. See [Debrid Services](/docs/downloads/debrid-services) for settings shared by all services, including the download order.

## Connecting Premiumize

1. Open **Settings → Debrid**, turn on **Use debrid services**, and find **Premiumize** under **Accounts**.
2. Click **Get an API key** to open your [Premiumize account](https://www.premiumize.me/account) page, and copy the API key shown there.
3. Paste it into **API key** and click **Connect**.

Ascendara checks the key before saving it. To disconnect, click **Disconnect**.

Once connected, the card shows your customer ID, plan, premium end date, and how much of your **Fair use** limit you've used.

## Fair Use

Premiumize limits downloads with fair use points rather than a fixed number of downloads. Each download uses points depending on the host and size, and the limit resets over time. When **Fair use used** gets close to 100%, Premiumize may refuse new downloads until it resets. See Premiumize's site for the current rules.

## Hosted Links

Supported hosted links are converted to direct links by Premiumize and then downloaded by Ascendara's normal downloader. Only links from hosts that Premiumize lists as supported are sent to it. Other links download normally.

Turn on **Prefer over direct downloads** to also send links from [Magic Link](/docs/features/magic-links) hosts through Premiumize.

## Torrents

With **Cloud torrents** on, magnet links are added to your Premiumize transfers instead of the built-in torrent client. When the transfer finishes (or is seeding), every file continues to your PC and installs like any other download.

While a torrent is in the cloud, it's listed in **Downloads** under **Waiting at your debrid service**. Premiumize has no page of its own in Ascendara. Ascendara checks on each torrent every 30 seconds.

Turn on **Remove from the service when done** to delete each transfer from Premiumize once its game is on your PC. See [Removing Finished Torrents](/docs/downloads/debrid-services#removing-finished-torrents).

## Troubleshooting

### "Premiumize could not download this torrent"
Premiumize reported an error for the transfer, for example because it has no seeders or you've reached your fair use limit. Remove it from **Downloads** and try another source.

### Downloads fail with a fair use error
Wait for your fair use limit to reset, or move another service above Premiumize in **Download order**.
