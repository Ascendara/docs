# Linux and macOS

Ascendara runs on Linux and macOS as well as Windows. Most games in the index are Windows games, so on Linux and macOS they run through a compatibility layer. Windows is still recommended for the best experience: when you first launch Ascendara on another platform, it shows a compatibility notice, and some features are Windows-only.

To install Ascendara, see [Installing Ascendara](/docs/getting-started/installation).

## Linux

On Linux, Windows games run through **UMU-Launcher**, **Proton**, or **Wine**. Native Linux executables run directly.

### Recommended Setup

During the welcome setup, Ascendara checks which tools you have and offers to install:

- **UMU-Launcher**: Runs games inside Steam's Linux Runtime container, the same way Steam does, and applies game-specific fixes (Protonfixes) automatically. Without it, some games may crash at startup.
- **Proton CachyOS** (recommended) or **Proton-GE**: Custom Proton builds with extra patches, DXVK, VKD3D-Proton, and FSR support for the best Windows game compatibility.

Proton found in your Steam installation also works with UMU. You can skip this step and install the tools later in **Settings**.

### How Ascendara Chooses a Runner

When you play a Windows game:

1. If UMU-Launcher is installed, the game runs through `umu-run`. Ascendara passes your selected Proton build, or lets UMU use UMU-Proton automatically if none is selected. Visual C++ redistributables are installed into the game's prefix automatically.
2. Without UMU-Launcher, the game runs directly with Proton (`proton run`).
3. If no Proton build is found, the game runs with Wine.

**Default Runner** in **Settings** is set to **Auto-detect (Recommended)**, which uses the first Proton build Ascendara finds, then Wine. You can choose a specific runner or select a custom one.

### Compatibility Settings

In **Settings**, the Linux compatibility section lets you:

- See the **Detected Runners** and where each comes from (**Steam**, **System**, or **Custom**)
- Choose the **Default Runner**
- Download, update, or reinstall Proton CachyOS, Proton-GE, UMU-Proton, and UMU-Launcher
- View the available Proton launch options, such as `PROTON_ENABLE_WAYLAND`, FSR, DLSS upgrades, or `PROTON_LOG`, and copy them into a game's launch options

### Per-Game Options

On a game's page:

- **UMU ID**: Links the game to its Protonfixes entry so the right fixes are applied. Ascendara tries to detect it automatically; you can look up IDs in the UMU database and set one manually.
- **Launch Options**: Steam-style launch options work, including environment variables such as `PROTON_LOG=1 %command%`.
- **Reset Compatibility Prefix**: Deletes the game's Windows prefix (its configuration, DLLs, registry entries, and temporary files) to start fresh. Your save files may be lost if they're stored inside the prefix, so back them up first.

### Linux Files

Ascendara stores its Linux tools and prefixes in `~/.ascendara`:

| Path | Contents |
| --- | --- |
| `~/.ascendara/compatdata/<game>/` | Each game's Wine/Proton prefix |
| `~/.ascendara/runners/` | Proton builds downloaded by Ascendara |
| `~/.ascendara/tools/umu/` | UMU-Launcher |
| `~/.ascendara/umu_database.csv` | Cached UMU database |
| `~/.ascendara/ludusavi` | Ludusavi for [game backups](/docs/features/game-backups) |

Settings are stored in `~/.config/ascendara/ascendarasettings.json`.

## macOS

On macOS, Windows games run through **Wine**. Native macOS executables and `.app` bundles can be selected for games and run directly.

During the welcome setup, Ascendara can install Wine through Homebrew. Homebrew must already be installed, and Apple Silicon Macs also need Rosetta 2. If setup asks for a password, run `brew install --cask wine-stable` in Terminal, then select **Check again**. Game compatibility under Wine varies from game to game.

### Not Available on macOS

- Proton and UMU-Launcher (Linux only)
- Switching between app branches (Live, Public Testing, Experimental)
- The Workshop Downloader (SteamCMD)
- Windows game dependency installers and Defender protection
- Importing from launchers other than Steam

Torrent downloads still require qBittorrent with its Web UI enabled. Ludusavi for game backups is downloaded by Ascendara, or Ascendara uses a `ludusavi` binary on your `PATH`.

Settings are stored in `~/Library/Application Support/ascendara/ascendarasettings.json`.

## Features That Are Windows-Only

On both Linux and macOS:

- **Protect Directories from Defender**
- **Game dependencies** installer (on Linux, UMU-Launcher installs Visual C++ redistributables into each game's prefix, and Protonfixes handles other missing components)
- **Version rollback** in the [System Center](/docs/features/system-center)
- **Desktop and Start Menu shortcuts**
- Importing from Epic Games, GOG Galaxy, Ubisoft Connect, and Battle.net (Steam import works everywhere). See [Importing Games](/docs/features/importing).

## Troubleshooting

### "No Compatibility Tool Detected"
Install Proton CachyOS or Proton-GE and UMU-Launcher from **Settings**, or install Wine with your package manager.

### A Game Crashes at Launch on Linux
- Install UMU-Launcher if it isn't installed.
- Set the game's UMU ID so Protonfixes are applied.
- Try a different Proton build as the default runner.
- Reset the game's compatibility prefix.

### Games Don't Start on macOS
Make sure Wine is installed and works from Terminal (`wine --version`). Some games don't run under Wine at all.
