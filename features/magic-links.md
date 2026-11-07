# Ascendara Magic Links

Ascendara Magic Links are download links Ascendara can handle on its own. Pick a Magic Link host, click **Download Now**, and Ascendara gets the real file from the host and starts the download. You never open the host's page, so there are no ads, popups, CAPTCHAs, or fake download buttons.

> **Note:** Magic Links used to be called **Seamless Downloads**. The feature was renamed to Ascendara Magic Links, and MegaDB and BuzzHeavier now work as Magic Links too.

## Supported Hosts

| Host | How it works |
| --- | --- |
| **GoFile** | Starts right away. |
| **PixelDrain** | Starts right away. |
| **BuzzHeavier** | Ascendara opens the host page in a hidden window and grabs the download. The window only appears if the host shows something you need to click through. |
| **MegaDB** | MegaDB makes every download wait through a short countdown (about 10 seconds). Ascendara waits it out for you, then starts the download. |

## Spotting a Magic Link

On a game's download page, Magic Link hosts are marked with a **Magic** chip and a wand icon in the **Download Source** list. Click the chip to see a short explanation of Magic Links. When a Magic Link host is selected, the card's title changes to **Magic Link Download**.

## Downloading With a Magic Link

1. Open a game and select a host with the **Magic** chip.
2. Click **Download Now**.
3. The button shows each step as it happens: opening the download page, the MegaDB countdown (with the seconds remaining), getting the download link, and starting the download.
4. The game is added to your downloads like any other download.

### While You Wait

While a Magic Link is being prepared, the **Download Source** list is replaced by a small game: help the cat dodge the ads. It starts on autopilot. Press **Space** or click the game to take over and jump. Your best score is saved on your device. The game goes away once the download starts.

## When a Magic Link Doesn't Work

- **MegaDB**: if Ascendara can't get the link directly (for example, if MegaDB changes its page), it falls back to the hidden window and shows it only if you need to click through something.
- **BuzzHeavier**: if the host shows a check Ascendara can't get past on its own, the window appears so you can finish it.
- **Ad blocking**: whenever a host window is shown, Ascendara blocks known ad hosts and popups in it. The list of ad hosts is kept up to date by the Ascendara API.
- **Anything else**: choose another host, or use the [browser extension](/docs/downloads/downloading#browser-extension).

## Magic Links and Debrid Services

If you use a [debrid service](/docs/downloads/debrid-services), Magic Link hosts keep using Magic Links by default. Turn on the service's **Prefer over direct downloads** option to send Magic Link hosts through the debrid service instead. You can also choose a service, or **None**, for a single download from the download page.

## Big Picture Mode

In [Big Picture Mode](/docs/features/big-picture), GoFile and PixelDrain Magic Links download directly. BuzzHeavier and MegaDB need the regular interface.
