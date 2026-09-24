# Ascendara Retro

Ascendara Retro is a separate library for organizing and launching games from supported classic consoles. It does not download ROMs or replace the emulator. You provide the ROM folders and emulator files, and Ascendara remembers the setup, scans the folders, matches game metadata, and starts the configured emulator with the correct game file.

## Supported consoles

The base console profiles, available to everyone:

- PlayStation 1: DuckStation
- PlayStation 2: PCSX2
- PlayStation 3: RPCS3
- PlayStation Portable: PPSSPP
- Nintendo GameCube and Wii: Dolphin
- NES, SNES, Nintendo 64, Game Boy, Game Boy Color, and Game Boy Advance: RetroArch

The suggested emulator is a launch preset. You may choose a custom emulator and custom arguments when a preset does not fit your setup.

## Expanded consoles (Ascend)

An active Ascend subscription or verified access unlocks additional console profiles and alternate emulator presets:

- Xbox: xemu
- Xbox 360: Xenia
- Sega Dreamcast, Sega Naomi / Naomi 2, and Atomiswave: Flycast
- Sega Genesis / Mega Drive, Sega CD, and Sega 32X: Gens
- Sega Saturn and PC Engine / TurboGrafx-16: Mednafen
- Nintendo DS / DSi: melonDS
- Nintendo 3DS: Azahar
- Nintendo Wii U: Cemu
- Nintendo Switch: Eden

Ascend access also unlocks alternate launch presets for consoles that are otherwise free, such as FCEUX for NES and DolphinXR for GameCube and Wii.

These consoles and presets are configured from **All emulators** on the Retro page. Without an active Ascend subscription or verified access, the section shows a prompt to sign in or subscribe instead of the console and emulator list; the locked consoles also show a lock icon in the console list and open the same prompt when selected.

**All emulators** also lists setup resources that are not console profiles themselves, such as PS3 system-software links, BIOS/setup guides for Azahar, Cemu, Eden, melonDS, and Mednafen, and ShadPKG for PS3. It additionally lists experimental, guide-only entries for PS4 and PS5 emulators (fpPS4, KytyPS5, RPCSX); these are not yet launchable consoles in Retro and only link to the emulator's own project pages.

## First-time setup

1. Open **Retro** and select a console.
2. Open that console's setup dialog.
3. Choose the emulator executable. On Windows, select the emulator's `.exe` file.
4. Add one or more folders containing that console's ROMs. Folders must exist and use absolute paths. You can add up to 32 folders.
5. For RetroArch, select the matching core. The setup dialog suggests a core for each supported system.
6. Choose the save or memory-card folder if you want local backups, cloud saves, or memory-card management.
7. Choose whether the emulator should start fullscreen.
8. Save the setup. Use **Save and scan** to scan the ROM folders immediately.

The setup is saved per console. Changing the setup or save folder requires the emulator and current Retro task to be closed.

### Custom emulator arguments

Choose the **Custom** launch preset, enter one argument per line, and use `{rom}` where the game file should be inserted. If `{rom}` is omitted, Ascendara appends the selected game file. Arguments are limited to 64 lines and 8,192 characters.

## Scanning and library matching

Choose **Scan** after adding or changing ROM folders. Ascendara checks the supported file extensions for that console, records discs and related files, and keeps the scan running in the background if you leave the page.

If the local catalogue has one unambiguous match, Ascendara fills in the title, year, genres, description, developer, and cover automatically. You can update the catalogue from the Retro page by downloading the catalogue or importing a LaunchBox `Metadata.xml` or `Metadata.zip` file.

For a game with an incorrect or missing match:

1. Open the game details.
2. Select **Edit match**.
3. Search the catalogue for the correct title, or edit the title and other details manually.
4. Save the details.

You can also set a cover image yourself. Covers must be PNG, JPEG, or WebP files smaller than 5 MB.

## Playing a game

Open a game and choose **Play**. Multi-disc games have a starting-disc selector. Ascendara validates the emulator, game file, RetroArch core, and referenced discs before launching. It passes the appropriate arguments for the selected emulator and records the last-played time, launch count, and play time.

Close the emulator before changing its setup, changing saves, scanning, or removing a game. Ascendara prevents conflicting save operations while an emulator is running.

Use **Show files** in the game details to open the game file's location. Removing a game removes it from the Retro library record; it does not delete the ROM files.

## Local save backups

Open a console's **Save management** dialog to:

- Save the complete configured save folder to a ZIP file.
- Restore a local ZIP backup.
- Open the recovery-copy folder.
- View and rename or duplicate PS1 and PS2 memory-card files.

Close the emulator on every device before backing up or restoring. Restore asks for confirmation before replacing matching local files and keeps a recovery copy of the previous files. A backup can contain saves for several games when the console shares one memory-card or save directory.

## Ascend cloud saves

Cloud saves require a signed-in account with an active Ascend subscription or verified access. The local save folder must be configured first.

In the cloud-save section:

1. Close the emulator.
2. Select **Back up to cloud** to upload the complete console save folder.
3. Use **Refresh** to load available backups.
4. Select **Restore** beside a backup to download and restore it.

Cloud backups are limited to 256 MB. Restore validates that the backup belongs to the selected console, asks before replacing local saves, and keeps a recovery copy. The same backups also appear in **Ascend → Cloud Backups**.

## Catalogue and library files

Retro stores its library and profiles in Ascendara's application-data directory under `retro/library.json`. The downloaded metadata catalogue is cached separately. Do not edit these files while Ascendara is running. If the library cannot be loaded, Ascendara preserves the original file and reports its path.

The ROMs, emulator installation, save folder, and cloud backup data remain separate from the Retro library record. Moving any of them requires returning to console setup and selecting the new path.

## Common problems

**The scan finds no games.** Check that the selected folder contains supported file extensions for the selected console. For PS3, use the installed game folder format expected by RPCS3; it does not use the same file-extension scan as disc-based consoles.

**The emulator will not launch.** Reopen setup and select the actual emulator executable. For RetroArch, select a core. Check the emulator's own BIOS, firmware, and compatibility requirements.

**A game has the wrong artwork or title.** Use **Edit match** to choose a catalogue result, enter manual details, or choose a cover image.

**Save backup or restore is unavailable.** Select a valid save or memory-card folder and close the emulator. Cloud saves additionally require Ascend access and a signed-in account.

**A restore changed the wrong files.** Open **Recovery copies** in Save management. Ascendara keeps the previous local files before a restore so they can be recovered.

**A task is still running after leaving Retro.** Scans and catalogue imports run in the main process and continue while you navigate elsewhere. Return to Retro to see progress and warnings.

**A console is locked or missing from the list.** Consoles under Expanded consoles (Ascend), and alternate presets such as FCEUX or DolphinXR, require an active Ascend subscription or verified access. Select the locked console, or open **All emulators**, to sign in or subscribe.
