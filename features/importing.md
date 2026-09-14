# Importing Games

Import games from other launchers to use Ascendara as a unified library. You can browse and launch your installed games alongside games downloaded through Ascendara.

Imported games remain managed by their original launcher. If a matching game is available in Ascendara's catalog, you can optionally switch to an Ascendara-managed download to enable version tracking and update checks.

## Imported and Ascendara-Managed Games

| | Imported game | Ascendara-managed game |
| --- | --- | --- |
| Installation | Uses the game already installed on your computer | Downloads a copy through Ascendara |
| Library access | Browse and launch from Ascendara | Browse and launch from Ascendara |
| Version tracking and update checks | Unavailable in Ascendara; use the original launcher for updates | Available in Ascendara |
| Game details label | **Not Ascendara-Managed** | **Managed** |

Importing adds a library entry without moving or downloading the game files. Ascendara scans local installation records and downloads available artwork. You do not need to sign in to your launcher accounts to import, but some games may still require their original launcher for DRM or sign-in when playing. Keep that launcher installed.

## Supported Launchers

On Windows, you can import from **Steam**, **Epic Games**, **GOG Galaxy**, **Ubisoft Connect**, and **Battle.net**. On other platforms, the importer supports **Steam**. Unsupported options appear as **Unavailable on this platform** and cannot be selected.

Only games installed on your computer are imported. Games you own in a launcher but have not installed will not appear in the scan.

## How to Import Games

1. Make sure the games you want to import have finished installing in their original launcher.
2. Choose a download directory in Ascendara's **Settings** if you have not already done so. Ascendara needs this directory to store library information and artwork, even when importing existing installations.
3. Open your **Library** and select **Import Games** in the sidebar.
4. Under **Choose launchers**, select one or more launchers to scan. Available launchers are selected by default.
5. Click **Scan and import**.
6. Wait for the three stages: **Scan installations**, **Add to library**, and **Find artwork**.
7. Review the results, then open an imported game from your Library to play.

Ascendara tries to detect each game's executable during import. If it cannot find one, choose the game's executable in the **Executable Manager** before playing.

### Running in the Background or Stopping

Click **Run in background**, or close the dialog, to keep browsing while the import continues. Keep Ascendara open. Reopen **Import Games** in the Library to check progress.

To cancel the remaining work, click **Stop queue**. The current scan or download finishes before the queue stops. Games already added stay in your library, although unfinished artwork may remain pending. Use **Scan again** to look for games that were not added.

### Understanding the Results

- **Found**: Installed games discovered in the selected launchers.
- **Added**: Games successfully added to your Ascendara library.
- **Skipped**: Games already in your library. Scanning again does not add another copy of these entries.
- **Failed**: Games that could not be added.

Each game also shows its artwork status. Missing images or a failed artwork download do not remove a successfully imported game. An import can finish with a few issues while still adding other games successfully.

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

Open the selected launcher and confirm that the game is fully installed and its drive is connected. Finish or repair incomplete installations, then return to Ascendara and click **Scan again**. Uninstalled games in your launcher account are not imported.

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
