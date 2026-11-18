# Ascendara Retro

Ascendara Retro is a tab in your [Library](/docs/features/library) for organizing and launching games from supported classic consoles. It does not download ROMs or replace the emulator. You provide the ROM folders and emulator files, and Ascendara remembers the setup, scans the folders, matches game metadata, and starts the configured emulator with the correct game file.

## Supported consoles

The base console profiles, available to everyone:

- PlayStation 1: DuckStation
- PlayStation 2: PCSX2
- PlayStation 3: RPCS3
- PlayStation Portable: PPSSPP
- Nintendo GameCube and Wii: Dolphin
- NES, SNES, Nintendo 64, Game Boy, Game Boy Color, and Game Boy Advance: RetroArch

The suggested emulator is a launch preset. You may choose a custom emulator and custom arguments when a preset does not fit your setup.

For a console that isn't listed, use [Other console](#other-consoles).

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

These consoles and presets are configured from **All emulators** in the Retro tab. Without an active Ascend subscription or verified access, the section shows a prompt to sign in or subscribe instead of the console and emulator list; the locked consoles also show a lock icon in the console list and open the same prompt when selected.

**All emulators** also lists setup resources that are not console profiles themselves, such as PS3 system-software links, BIOS/setup guides for Azahar, Cemu, Eden, melonDS, and Mednafen, and ShadPKG for PS3. It additionally lists experimental, guide-only entries for PS4 and PS5 emulators (fpPS4, KytyPS5, RPCSX); these are not yet launchable consoles in Retro and only link to the emulator's own project pages.

## Adding games

The quickest way to start is **Add games** at the top of the Retro tab. You don't need to set up each console first:

1. Choose **Add games** and add the folders your games are in.
2. Retro looks through the folders and lists what it found, for example "12 files (.cue, .chd)", with the console it thinks they're for. Disc images (`.iso`) are identified from their headers, so GameCube, Wii, PSP, and PlayStation discs in the same folder are told apart. Change the console for any group, or choose **Don't add**. For a system Retro doesn't list, choose **Other console** and give it a name.
3. Under **Emulators**, choose each console's emulator executable. Retro recognizes common emulators, such as DuckStation, PCSX2, Dolphin, PPSSPP, and RetroArch, and sets them up with the right launch preset. For RetroArch it also finds matching cores in its `cores` folder. Any other emulator is added as a custom emulator. One emulator is applied to every found console it supports, so you only pick RetroArch once.
4. Choose **Add and scan**. Retro saves each console's setup, adds the file types it found, and scans everything.

Consoles that are already set up keep their emulator unless you change it. You can skip choosing an emulator and set it up later from the console's setup.

## First-time setup

To set up one console by hand instead:

1. Open **Library** → **Retro** and select a console.
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

### Extra file types

Each console's setup lists the file types it scans for. To scan other types, for example when your emulator reads a format Retro doesn't list, enter them under **Extra file types**, separated by commas: `nsp, xci`. The leading dot is optional. You can add up to 32 types, using letters and numbers only. Extra file types work with any emulator preset, including **Custom emulator**.

When a scan finds no games, Ascendara tells you which file types it did find in the folders and which consoles read them. For example, a folder of `.nsp` files scanned as NES shows that they're Nintendo Switch games.

## Other consoles

**Other console**, under **Other systems** in the console list, is for any system Retro doesn't list, such as the Nintendo Switch 2. It's available to everyone.

1. Select **Other console** and open its setup.
2. Optionally give it a name, such as `Nintendo Switch 2`. The name replaces "Other console" in the console list.
3. Choose your emulator's executable. Keep the **Custom emulator** preset, and add [launch arguments](#custom-emulator-arguments) if the emulator needs them.
4. Enter the file types your emulator opens under **Extra file types**, for example `xci, nsp`. Other console has no file types of its own, so a scan finds nothing until you add some.
5. Add your game folders and choose **Save and scan**.

Games on Other console aren't matched against the metadata catalogue. Set their titles and covers yourself from the game details.

Use Other console for one system at a time. Games for a console Retro does list belong under that console, so they get the right launch preset and catalogue matches.

When games are added, Ascendara looks each one up in the LaunchBox games database and fills in the title, year, genres, description, developer, and cover automatically. Only the games in your library are looked up, so there's nothing to download first. Games without a match are checked again after a week. The **Catalogue** tab searches the same database online.

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

Retro stores its library and profiles in Ascendara's application-data directory under `retro/library.json`. Game metadata is saved with each game in the library. Do not edit these files while Ascendara is running. If the library cannot be loaded, Ascendara preserves the original file and reports its path.

The ROMs, emulator installation, save folder, and cloud backup data remain separate from the Retro library record. Moving any of them requires returning to console setup and selecting the new path.

## Common problems

**The scan finds no games.** Read the message shown after the scan: it lists the file types in the folder and the consoles they belong to. Set up the matching console, or add the file type under [Extra file types](#extra-file-types). For a console Retro doesn't list, use [Other console](#other-consoles). For PS3, use the installed game folder format expected by RPCS3; it does not use the same file-extension scan as disc-based consoles.

**The emulator will not launch.** Reopen setup and select the actual emulator executable. For RetroArch, select a core. Check the emulator's own BIOS, firmware, and compatibility requirements.

**A game has the wrong artwork or title.** Use **Edit match** to choose a catalogue result, enter manual details, or choose a cover image.

**Save backup or restore is unavailable.** Select a valid save or memory-card folder and close the emulator. Cloud saves additionally require Ascend access and a signed-in account.

**A restore changed the wrong files.** Open **Recovery copies** in Save management. Ascendara keeps the previous local files before a restore so they can be recovered.

**A task is still running after leaving Retro.** Scans and metadata lookups run in the main process and continue while you navigate elsewhere. Return to Retro to see progress and warnings.

**A console is locked or missing from the list.** Consoles under Expanded consoles (Ascend), and alternate presets such as FCEUX or DolphinXR, require an active Ascend subscription or verified access. Select the locked console, or open **All emulators**, to sign in or subscribe.
