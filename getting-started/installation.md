# Installing Ascendara

## System Requirements
- **Windows 10 or 11** (64-bit), **Linux** (x64), or **macOS** (Apple Silicon or Intel)
- A stable internet connection
- Enough free space for your games. Ascendara warns you during setup if the selected drive has less than 40 GB free.
- Optional: a Chromium-based browser or Firefox for the [browser extension](#browser-extension)

Ascendara must not be run as administrator on Windows. Running it in administrator mode causes path problems, so the app will ask you to restart it normally.

## Windows

1. Visit [ascendara.app](https://ascendara.app) and click the download button, or download the installer directly from [cdn.ascendara.app/files/AscendaraInstaller.exe](https://cdn.ascendara.app/files/AscendaraInstaller.exe).
2. Run the installer and follow the setup wizard.
3. Launch Ascendara and complete the [first-time setup](/docs/getting-started/usage#first-time-setup).

### About the Installer

The Ascendara installer is a small, open-source app ([source on GitHub](https://github.com/Ascendara/installer)) that always downloads the latest version. If you need a specific version, download its setup file from the [GitHub releases page](https://github.com/Ascendara/ascendara/releases).

### SmartScreen Warning

Windows Defender SmartScreen may flag the installer because Ascendara's installer isn't signed with a paid code-signing certificate. You can safely proceed by clicking **More info** → **Run anyway**.

#### Why Does This Happen?

Windows shows this warning for executables that aren't signed with a certificate trusted by Microsoft. These certificates are expensive every year, and like many open-source projects, Ascendara does not purchase one so that the app can stay free.

Instead, Ascendara stays transparent: the source code is public, and each [GitHub release](https://github.com/Ascendara/ascendara/releases) lists the SHA-256 checksums of its installers so you can verify what you downloaded.

## Linux

Run the install script in a terminal:

```bash
curl -fsSL https://ascendara.app/install.sh | bash
```

The script downloads the latest AppImage to `~/.local/bin/Ascendara.AppImage`, installs an icon, and creates a desktop entry so Ascendara appears in your application menu and can handle `ascendara://` links. You can also download the `.AppImage` from the [GitHub releases page](https://github.com/Ascendara/ascendara/releases), make it executable with `chmod +x`, and run it.

Windows games run on Linux through a compatibility tool. During setup, Ascendara checks for Proton, UMU-Launcher, and Wine and offers to install Proton CachyOS (recommended) and UMU-Launcher for you. See [Linux and macOS](/docs/features/linux-and-macos).

Automatic updates on Linux replace the installed AppImage, so run Ascendara from the AppImage the installer created rather than from a temporary copy.

## macOS

Download the `.dmg` for your Mac from the [GitHub releases page](https://github.com/Ascendara/ascendara/releases):

- `Ascendara-<version>-mac-arm64.dmg` for Apple Silicon
- `Ascendara-<version>-mac-x64.dmg` for Intel Macs

Open the DMG and drag Ascendara into **Applications**. Install it in a writable location such as **Applications**, because automatic updates replace the installed app.

Windows games on macOS require Wine. During setup, Ascendara can install Wine through Homebrew (Homebrew must already be installed, and Apple Silicon also requires Rosetta 2). Game compatibility varies. See [Linux and macOS](/docs/features/linux-and-macos) for the features that are not available on macOS.

## Browser Extension

The Ascendara Download Handler sends downloads from your browser straight to Ascendara:

- Captures the download when it starts and opens Ascendara with the file link
- Lets you choose which downloads it captures: **My sites and providers**, **Any website**, or **Capture next download**
- Works with games that aren't in the Ascendara index: you enter the game's name in Ascendara and continue to the normal download flow

Visit [ascendara.app/extension](https://ascendara.app/extension) to be directed to the correct store for your browser. The extension supports Chromium-based browsers (Chrome, Edge, Opera, Brave) and Firefox. To load it manually from source, see [Importing the Extension](/docs/developer/importing-from-source).

## After Installing
1. Complete the welcome setup (download folder, theme, game index, and dependencies).
2. Install the browser extension for your preferred browser.
3. On Windows, enable **Protect Directories from Defender** if you use Windows Defender.
4. Review your preferences in **Settings**.
