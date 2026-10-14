# Game Backups
Ascendara integrates [Ludusavi](https://github.com/mtkennerly/ludusavi), a tool written in Rust for backing up PC game save data. Ludusavi knows where thousands of games store their saves, and Ascendara uses its command-line interface to back up and restore your progress.

For classic console saves, see [Ascendara Retro](/docs/features/retro#local-save-backups), which has its own save management.

## How It Works

1. **Backup creation**: Ascendara runs Ludusavi to find and back up the save files and configuration for a game.
2. **Restoration**: Ascendara runs Ludusavi to restore a backup to the game's save location.
3. **Automatic backups**: The [Game Handler](/docs/binary-tool/game-handler) runs a backup when you close a game that has auto-backup enabled.

For details on Ludusavi's CLI, see the [official Ludusavi documentation](https://github.com/mtkennerly/ludusavi/blob/master/docs/cli.md).

## Setting Up Backups

1. Open **Settings** and find **Game Backups**.
2. Choose a **Backup Location**. Backups are disabled until a location is selected.
3. Adjust the backup options if you want (see [Backup Settings](#backup-settings)).

Ludusavi is installed as a sidecar the first time you need it. If it isn't installed, Ascendara asks to install it automatically. On Windows it's stored with Ascendara's resources, and on Linux and macOS in `~/.ascendara/ludusavi`. On macOS, Ascendara also uses a `ludusavi` binary found on your `PATH`.

## Backing Up a Game

Open a game's page from your Library and select **Save Backups**. The backup dialog lets you:

- **Back up now**: Create a backup of the game's saves immediately.
- **Restore Latest Backup**: Restore the most recent backup.
- **Choose Backup to Restore**: Pick a specific backup by date.
- **Restore from cloud**: Restore a backup stored in your Ascend account, including on a new PC.
- **Open Backup Folder**: Browse the backups stored on this PC.
- **Auto-backup on Game Close**: Back up automatically every time the game closes.

> ⚠️ **Warning**: Restoring a backup overwrites the game's current save files. Make sure the game is closed before restoring.

### Custom Save Paths

If Ludusavi doesn't find a game's saves, open **Backup settings** in the backup dialog and add the exact folders the game uses under **Custom Save Paths**. Leave the list empty to use Ludusavi's automatic detection. You can also manage custom save paths, auto-backup, and launch options together in [Game Profiles](/docs/features/library#game-profiles).

On Linux, if a game has no custom save paths, Ascendara tells Ludusavi to look inside the game's Wine or Proton prefix.

## Cloud Backups

With an active [Ascend](/docs/features/ascend) subscription, you can upload backups to your Ascend account and restore them on another PC:

1. Back up the game on your first PC.
2. Turn on **Always back up to cloud** in the game's backup settings. Each backup you create with **Back up now** is then also uploaded. Enable this on each PC you use.
3. On your other PC, open the same game's backup dialog and select **Restore from cloud**.

If cloud backup is off for a game, Ascendara asks whether to upload when you back up. You can choose to always back up that game locally. Your uploaded save files are also listed in **Ascend** → **Save Backups**, where you can download or delete them.

## Library Backups

Select **Library Backups** in the Library sidebar to work with your whole library at once:

- **Back up library**: Back up saves for every game in your library, including games in folders. Games with no detected saves are skipped. You can upload each new backup to the cloud automatically.
- **Restore library**: Restore the latest local backup for each game. Games without a local backup are skipped.
- **Import from cloud**: Download selected cloud backups into your local Ludusavi backup folder. Then use **Restore library** to apply them.

Viewing a game's local backups is available to everyone. Backing up or restoring the whole library and managing cloud backups require an Ascend membership. Long operations can run in the background.

## Backup Settings

Configure these in **Settings** → **Game Backups**.

### Backup Format
- **ZIP (Recommended)**: Creates a compressed ZIP archive, ideal for saving space and creating portable backups.
- **Simple (Directory structure)**: Stores backups as plain files, which is faster and makes individual files easier to access.

### Compression Level
When using ZIP format, choose a balance between speed and size:
- **None (Fastest, largest files)**
- **Deflate (Balanced, recommended)**: The default
- **BZip2 (Better compression, slower)**
- **Zstd (Best compression, slowest)**

### Backups to Keep per Game
Choose **3**, **5** (default), **10**, or a custom number. When the limit is reached, Ludusavi removes the oldest full backup. This limit applies to every game.

### Skip Manifest Check
Runs Ludusavi with `--no-manifest-update`, so it doesn't download the latest version of its game database before backing up. Only enable this if backups are failing because of the manifest update and you know what you're doing.

## Benefits

- **Wide coverage**: Ludusavi's game database covers a large number of PC games across launchers.
- **Storage control**: Choose the format, compression, and how many backups to keep.
- **Automation**: Back up automatically when a game closes.
- **Portability**: Move saves between PCs with Ascend cloud backups.
- **One place**: Manage backups from Ascendara without switching to external tools.

## Troubleshooting

### Save Backups Is Disabled
Select a backup location in **Settings** → **Game Backups**.

### No Save Data Found
Ludusavi didn't find saves for the game. Play the game until it creates a save, then try again, or add the save folder under **Custom Save Paths**.

### Cloud Backup Is Unavailable
Cloud backups require a signed-in Ascend account with an active subscription.

### Restore Didn't Change My Save
Make sure the game was closed during the restore and that the backup is from the expected date. Some games also sync saves with their own cloud service, which can overwrite a restored save when the game starts.
