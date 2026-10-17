# Importing Games

Already have games on Steam, Epic Games, or another launcher? You can bring them into Ascendara so every game you play is in one library, next to the games you download through Ascendara.

Importing never moves, copies, or re-downloads your games. Each game stays where its launcher installed it, and that launcher keeps handling updates.

## Two Ways to Import

Open your **Library**, click the arrow next to **Add game**, and choose **Import from other launchers**. Ascendara asks which kind of games you want to bring in. The same choice is offered during first-time setup, on the **Optional extras** step.

| | [Games installed on this PC](#games-installed-on-this-pc) | [Games you own but have not installed](#store-accounts) |
| --- | --- | --- |
| What it finds | Games already downloaded with Steam, Epic Games, GOG Galaxy, Ubisoft Connect, or Battle.net | Every game you own on Steam, Epic Games, or GOG, installed or not |
| Do you need to sign in? | No | Yes, directly with each store |
| Can you play right away? | Yes, from your Library | Installed games start through their store; others open the store so you can install them |
| Best for | Getting your existing games into Ascendara quickly | Seeing your whole collection in one place |

**Not sure which to pick?** Start with **Games installed on this PC**. It's quick, needs no accounts, and you can connect your stores later. You can also use both: if a game shows up both ways, the installed copy is shown and the duplicate is hidden.

## Games Installed on This PC

This option looks at the launchers on your computer, finds the games they have installed, and adds them to your Library with artwork. You don't need to sign in to anything.

### Supported Launchers

On Windows, you can import from **Steam**, **Epic Games**, **GOG Galaxy**, **Ubisoft Connect**, and **Battle.net**. On other platforms, the importer supports **Steam**. Unsupported options appear as **Unavailable on this platform** and cannot be selected.

Only games installed on your computer are found. To see games you own but haven't installed, use [Store Accounts](#store-accounts) instead.

### How to Import Installed Games

1. Make sure the games you want have finished installing in their original launcher.
2. Choose a download directory in Ascendara's **Settings** if you have not already done so. Ascendara uses this directory to store library information and artwork, even for imported games.
3. In your **Library**, click the arrow next to **Add game** and choose **Import from other launchers**.
4. Choose **Games installed on this PC**.
5. Under **Choose launchers**, select the launchers you use. Available launchers are selected by default.
6. Click **Scan and import**.
7. Wait for the three stages: **Scan installations**, **Add to library**, and **Find artwork**.
8. Review the results, then open an imported game from your Library to play.

Ascendara tries to find each game's executable (the file that starts the game) during import. If it can't find one, choose it in the **Executable Manager** before playing.

Keep the original launcher installed. Some games still need it running to sign in or to check that you own the game (DRM).

### Running in the Background or Stopping

Click **Run in background**, or close the dialog, to keep browsing while the import continues. Keep Ascendara open. While a scan is running, the import options show **A scan is running**; choose **Games installed on this PC** again to check progress.

To cancel the remaining work, click **Stop queue**. The current scan or download finishes before the queue stops. Games already added stay in your library, although unfinished artwork may remain pending. Use **Scan again** to look for games that were not added.

### Understanding the Results

- **Found**: Installed games discovered in the selected launchers.
- **Added**: Games successfully added to your Ascendara library.
- **Skipped**: Games already in your library. Scanning again does not add another copy.
- **Failed**: Games that could not be added.

Each game also shows its artwork status. Missing images or a failed artwork download do not remove a successfully imported game. An import can finish with a few issues while still adding other games successfully.

## Store Accounts

Store Accounts let you see **every game you own** on **Steam**, **Epic Games**, and **GOG**, including games you have never installed. They appear in your Library as regular game cards with a small store badge, so your whole collection is in one place.

Ascendara doesn't download owned games itself. When you want to play one, Ascendara hands off to its store: installed games start through their launcher, and games that aren't installed open the store so you can install them there.

### Connecting a Store

1. In your **Library**, click the arrow next to **Add game**, choose **Import from other launchers**, then choose **Games you own but have not installed**.
2. Click **Connect** next to Steam, Epic Games, or GOG.
3. A sign-in window from that store opens. Sign in the same way you would on the store's website, including any two-factor code or "I'm not a robot" check.
4. When sign-in finishes, the window closes on its own and Ascendara loads your library. Large collections can take a moment.
5. Your owned games now show in your Library. Ascendara turns on **Show connected-store games** for you the first time you connect a store. Click **View in library** to see them.

Each connected store shows a green dot, your account name, how many games you own, and when it was last updated. You can connect one, two, or all three stores.

> **Tip:** The sign-in window only opens that store's own pages. If it seems stuck, close it and click **Connect** again. Sign-in stops on its own after 10 minutes.

### Choosing What Shows in Your Library

- **Show connected-store games** turns all owned games on or off in your Library at once. Your accounts stay connected either way.
- The switch on each store's row shows or hides just that store's games. For example, keep Steam visible and hide GOG.
- If a game is already installed (downloaded with Ascendara, added yourself, or imported from this PC), the installed copy is shown and the owned-game duplicate is hidden.

The bottom of the Store Accounts window tells you how many owned games are currently in your Library.

### Playing and Installing Owned Games

Click an owned game in your Library to open its page. It shows the game's description, screenshots, developer, and publisher, plus buttons that depend on whether the game is installed:

- **Play** appears when the game is installed through its store. Ascendara starts it through Steam, the Epic Games Launcher, or GOG Galaxy.
- **Install with Steam** or **Install with Epic Games** opens that launcher's install screen for the game. **Install with GOG** opens the game in your GOG account so you can download it.
- **Open in Steam**, **Open in Epic Games**, or **Open in GOG** takes you to the game's store page or library entry.

Ascendara checks whether a game is installed each time you open its page. Automatic install detection works on Windows for all three stores, and for Steam on other platforms. Where detection isn't available, you can still open the store to install or play.

After installing an owned game, you can also run [Games installed on this PC](#games-installed-on-this-pc) to add it as a regular imported game.

### Keeping Your Library Up to Date

Bought something new? Click **Refresh** on that store's row to reload your owned games. Ascendara uses your saved connection, so you only need to sign in again if the store has signed you out.

### Privacy and Security

- You sign in **directly with each store** in a separate, locked-down window that can only open that store's pages. Ascendara doesn't save your password.
- After sign-in, Ascendara keeps only what it needs to reload your library: your account name, your list of owned games, and the store's sign-in session. This is **encrypted on your computer** using your system's secure storage (Windows data protection, the macOS Keychain, or your Linux keyring).
- Store connections are kept separate from your Ascendara settings, library backups, and [Ascend](/docs/features/ascend) cloud sync, and they are never uploaded.
- If your system's secure storage isn't available, Ascendara won't connect accounts at all rather than saving them unprotected.

### Disconnecting a Store

Click **Disconnect** on the store's row. Ascendara deletes its saved connection and owned-games list for that store, and those games disappear from your Library. Games installed through the store are not affected, and you can reconnect at any time.

To switch to a different account on the same store, disconnect first, then connect again with the other account.

## Imported and Ascendara-Managed Games

Games you import from this PC are labeled **Not Ascendara-Managed**, because their original launcher still manages them.

| | Imported game | Ascendara-managed game |
| --- | --- | --- |
| Installation | Uses the game already installed on your computer | Downloads a copy through Ascendara |
| Library access | Browse and launch from Ascendara | Browse and launch from Ascendara |
| Version tracking and update checks | Unavailable in Ascendara; use the original launcher for updates | Available in Ascendara |
| Game details label | **Not Ascendara-Managed** | **Managed** |

If a matching game is available in Ascendara's catalog, you can optionally switch to an Ascendara-managed download to enable version tracking and update checks.

## Switching to an Ascendara-Managed Game

Switching lets Ascendara track the downloaded version, check for updates, and offer game updates when they are available in its catalog. This is optional: you can keep using your imported games as launcher-managed installations for as long as you want.

**Switching requires a download through Ascendara.** It does not convert the existing launcher's installation in place or simply turn on version tracking for those files.

1. Open the imported game's details from your **Library**.
2. Find the **Not Ascendara-Managed** card.
3. If Ascendara finds a catalog match, click **Switch & Download**. You can also start this process from the managed-copy link beside a matched game in the import results.
4. On the download page, review the matched game and its version, choose your download options, and start the download.
5. Wait for the download and installation to finish before playing the managed copy.

Opening the download page does not immediately remove your imported entry. Ascendara removes that entry when the actual download starts; for a queued download, this happens when the queue starts it. The new download then follows the normal Ascendara installation process.

Removing the imported entry does not uninstall the original launcher's game files. The managed download needs its own disk space. The switch also does not perform a save-data migration; see [Game Backups](/docs/features/game-backups) for backup and restoration options.

Once installed, the Ascendara-managed copy shows the **Managed** badge. Version tracking and update checks apply to this copy. Updates depend on what is available in Ascendara's catalog.

### Why Is There No Switch Option?

The switch option appears only when Ascendara finds a matching game in its catalog. If a match is unavailable or the lookup fails, you can still use the imported game normally. The import dialog shown during initial setup does not offer switching; open the game from your Library after setup instead.

## Troubleshooting

### No Installed Games Found

Open the selected launcher and confirm that the game is fully installed and its drive is connected. Finish or repair incomplete installations, then return to Ascendara and click **Scan again**. A scan doesn't find games you own but haven't installed; connect a [store account](#store-accounts) to see those.

### A Launcher Could Not Be Fully Scanned

Some installation records may be unreadable or incomplete. Open that launcher, finish or repair the affected installation, and scan again. Valid games from that launcher and other selected launchers can still be imported.

### A Game Has No Artwork

Check its artwork status in the results. Ascendara may not find matching images, or a download may fail. The game remains in your library and does not need artwork to launch.

### An Imported Game Will Not Launch

Make sure the original installation is still available and check the selected executable in the **Executable Manager**. Open the original launcher if the game needs it for sign-in or DRM.

### Version Tracking or Updates Are Unavailable

Check whether the game is marked **Not Ascendara-Managed**. Importing alone does not enable these features. Use the original launcher to update that installation, or choose **Switch & Download** when a catalog match is available.

### Import Will Not Start

Choose at least one supported launcher and make sure a download directory is set in **Settings**. If another import is running, let it finish or stop its queue first. If import status is unavailable, click **Try again**; restart Ascendara if the problem continues.

### Owned Games Don't Appear in My Library

Open **Store Accounts** and check that:

- the store shows a green dot and a game count,
- **Show connected-store games** is on,
- the switch on that store's row is on.

Games you already have installed appear as their installed copy instead, so they won't show up twice.

### Steam Is Connected but No Games Were Found

Steam only shares your library when your profile's **Game details** aren't private. In Steam, open your profile, choose **Edit Profile → Privacy Settings**, and make sure **Game details** is not set to **Private**. Then click **Refresh** on the Steam row.

### "Your Session Expired" or "Reconnect"

Stores sign you out after a while, or when you change your password. Click **Reconnect** on that store's row and sign in again.

### "The Store Account Changed"

Refresh found a different account than the one you connected. To use the other account, click **Disconnect**, then **Connect** and sign in with it.

### "Secure Storage Is Unavailable"

Ascendara protects store connections with your system's secure storage. On Linux, make sure a keyring (such as GNOME Keyring or KWallet) is installed and unlocked, then restart Ascendara. On Windows and macOS, signing in to your computer normally unlocks it.

### "Saved Account Could Not Be Unlocked"

Your system's secure storage changed or is locked, so Ascendara can't read the saved connection. Unlock your keyring and restart Ascendara, or click **Disconnect** and connect the store again.

### Play or Install Doesn't Open the Store

Make sure the store's launcher (Steam, the Epic Games Launcher, or GOG Galaxy) is installed and signed in. If the launcher isn't installed, Ascendara opens the store's website in your browser instead.
