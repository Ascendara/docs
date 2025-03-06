# Game Backups
Ascendara integrates [Ludusavi](https://github.com/mtkennerly/ludusavi), a specialized tool written in Rust specifically designed for backing up PC video game save data. Ludusavi provides robust CLI functionality that Ascendara leverages to protect your game progress and save files.

## How It Works

Ascendara interfaces with Ludusavi's powerful CLI capabilities to provide comprehensive backup functionality:

1. **Backup Creation**: When triggered, Ascendara calls Ludusavi's CLI commands to create thorough backups of game saves, configurations, and other critical game data.
2. **Restoration**: Users can restore game save data using Ascendara's interface, which invokes Ludusavi's restoration commands.
3. **Automatic Backups**: Ascendara can be configured to automatically run Ludusavi's backup command when exiting a game.

For detailed information on Ludusavi's CLI usage and capabilities, refer to the [official Ludusavi documentation](https://github.com/mtkennerly/ludusavi/blob/master/docs/cli.md).

## Using Game Backups in Ascendara

### Library Integration

Game backup functionality is seamlessly integrated into Ascendara's library interface:

1. **Enable Backups**: First, ensure that backups are enabled in Ascendara's settings.
3. **Backup Options**: Clicking the backup icon on a game in your library shows a menu with three options:
   - **Backup Now**: Creates an immediate backup of the game's save data
   - **Restore Backup**: Allows you to select and restore a previous backup
   - **Auto-Backup**: Toggle automatic backups that run when you exit the game

> ⚠️ **Warning**: Restoring a backup will overwrite your game save with the latest backup.

## Key Features

### Backup Creation

Create detailed backups of your game saves and configurations with a single click, powered by Ludusavi's efficient scanning and backup processes.

### Data Restoration

Easily restore your game save data from previous backups using Ludusavi's restoration capabilities, ensuring you never lose progress.

### Auto-Backup on Close

Automatically trigger Ludusavi backups when you exit a game, providing constant protection for your game save data.

### Customizable Backup Settings

Ascendara provides extensive customization options for your game backups:

#### Backup Format
Choose between two backup formats:
- **ZIP Archive**: Creates a compressed ZIP file containing all backup data, ideal for saving space and creating portable backups.
- **Simple Directory**: Stores backups as plain files in a directory structure, offering faster backups and easier manual access to individual files.

#### Compression Level
When using ZIP format, select from multiple compression levels to balance between backup speed and storage efficiency:
- **None (Fastest, largest files)**: No compression, resulting in the fastest backup process but largest file sizes.
- **Deflate (Balanced, recommended)**: The default option providing a good balance between compression ratio and speed.
- **BZip2 (Better compression, slower)**: Higher compression ratio than Deflate but requires more processing time.
- **Zstd (Best compression, slowest)**: Maximum compression resulting in the smallest backup files but taking the longest to process.

#### Backup Retention
Control how many backups are kept per game:
- Set a specific number of backups to retain (e.g., 3, 5, 10)
- Older backups are automatically removed when the limit is reached
- Configure different retention policies for different games based on importance

## Benefits

- **Comprehensive Coverage**: Ludusavi's extensive game database ensures wide-ranging support for various PC game titles across different platforms and launchers.
- **Efficient Storage**: Ludusavi's intelligent backup system minimizes storage usage while maintaining data integrity.
- **Cross-Platform Support**: Ludusavi's Rust-based architecture provides reliable performance across different operating systems.
- **Customization**: Leverage Ludusavi's flexible configuration options through Ascendara's interface for tailored backup solutions.
- **Storage Optimization**: Fine-tune compression settings to balance between backup speed and disk space usage.
- **Automated Management**: Set-and-forget backup retention policies prevent your storage from being overwhelmed with old backups.
- **One-Click Access**: Manage all your game backups directly from Ascendara's library without switching to external tools.

## Development Status

The game backup functionality in Ascendara is currently under active development. While the core features are already implemented and available for use, additional enhancements and refinements are planned for future releases.

Interested users are encouraged to provide feedback or contribute to the development of this feature. For information on how to contribute to Ascendara, please refer to the [Contributing Guide](/docs/getting-started/contributing).