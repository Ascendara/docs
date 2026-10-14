# Ascendara Companion

Ascendara Companion lets you follow and manage your Ascendara downloads from your phone, tablet, or another computer, and see what your Ascend friends are playing. Companion runs in your browser at [webview.ascendara.app](https://webview.ascendara.app) and can be installed as an app on your device.

Companion is an [Ascend](/docs/features/ascend) feature. You need to be signed in to Ascend on your PC.

## Connecting a Device

1. In Ascendara, open **Ascend** → **Companion**.
2. Select **Connect a device** (**Start Connection**). Ascendara generates a temporary connection code and a QR code.
3. On your other device, scan the QR code, or open [webview.ascendara.app](https://webview.ascendara.app) and enter the code.
4. Your device appears under **Connected Devices** once it's connected.

The code expires after a few minutes (the countdown is shown next to it). If it expires, generate a new one. You can connect as many devices as you want.

## What You Can Do From Companion

- See your active downloads and their progress, speed, and status
- **Pause**, **resume**, and **stop** downloads
- See what your Ascend friends are playing

While a download is running, Ascendara sends its progress to Companion every few seconds.

## Managing Connected Devices

**Ascend** → **Companion** lists your connected devices and when each was last active. Select **Disconnect device** to remove a device's access, or refresh the list to update it.

## Security

Communication between Companion and Ascendara is end-to-end encrypted. Commands from Companion are sent through a secure API, and your Ascendara app decides whether and how to run them. Companion can only control downloads; it can't access your files or settings.

## Troubleshooting

### The Code Doesn't Work
- Check that you entered the code exactly as shown.
- Make sure the code hasn't expired. Generate a new one if needed.
- Make sure you're signed in to Ascend on your PC with an active subscription or trial.

### Downloads Don't Appear or Update
- Keep Ascendara running on your PC and signed in to Ascend.
- If a pause, resume, or stop command isn't applied, open the **Downloads** page in Ascendara on your PC.

For developers, see [Webview Development](/docs/developer/webview).
