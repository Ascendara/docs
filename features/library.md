# Your Library

The **Library** brings together every game you play with Ascendara: games downloaded through Ascendara, custom games you add yourself, and games [imported](/docs/features/importing) from other launchers. Games appear in the Library while they're still downloading.

## Library Tabs

The Library sidebar has these tabs. Drag them to change their order.

- **All Games**: Every installed game and your folders.
- **Favorites**: Games you marked with the heart icon. You can favorite games before you install them, give them a personal 1–5 star rating, and set a status (**Playing**, **Completed**, or **Backlog**). Favorite ratings are stored only on your computer and are never shared.
- **Cloud Games**: Games saved to your [Ascend](/docs/features/ascend) cloud library that aren't installed on this PC. This tab appears when you're signed in to Ascend. Select **Download & Restore** (or **Add & Restore** for custom games) to install a game and recover its playtime.
- **Play Later**: Games you saved to play later from Search or a game page.
- **History**: Games you deleted while keeping their data. Their playtime and stats are preserved, and are restored if you reinstall the game.
- **Retro**: Your [Ascendara Retro](/docs/features/retro) library.

The sidebar also has tools to add games, create folders, [import games](/docs/features/importing), sync to the cloud, open [Library Backups](/docs/features/game-backups#library-backups), and calculate your library value.

## Adding Games

Select **Add to Library** in the sidebar.

- **Single game**: Choose an executable, then enter the game's name, version, and whether it includes online play or DLC.
- **Scan folder**: Choose a folder whose subfolders each contain a game. Ascendara finds the executables, lets you review the names, and adds the selected games. If a folder has several executables, choose the right one.

You can also drag and drop a game's `.exe` onto the Library. Adding a game never modifies its files.

## Organizing Your Library

- **Folders**: Create folders with **New Folder** and move games into them with **Move to folder…**. You can rename, hide, or remove folders. Removing a folder moves its games back to the main library. Hidden folders appear in their own tab.
- **Sorting and grouping**: Sort by name or most played, put folders first or last, and group games by directory.
- **Multiselection**: Select several games at once to remove them together.
- **Search**: Press **Ctrl+F** in the Library to search your games.

### Smart Collections

Smart Collections are automatic views built from data Ascendara already tracks: **Continue Playing**, **Never Played**, **Played**, **Custom Games**, **Online**, **VR**, and **DLC**. Open them from the command palette (**Ctrl+K**) with **Open Smart Collections**.

### Duplicate Installs

If the same game is in your library more than once (for example, downloaded through Ascendara and imported from Steam), Ascendara asks whether to keep all entries or keep one and hide the others. Your game files stay on disk either way. You can review your choices later from the Library.

## Playing Games

Open a game and select **Play**. Ascendara launches the game through the [Game Handler](/docs/binary-tool/game-handler), tracks your playtime, and updates Discord Rich Presence. By default, Ascendara hides its window while you play (**Hide on Game Launch** in **Settings**).

- If a game has several executables, Ascendara asks which one to run.
- If a game closes right after launching, Ascendara suggests running the executable directly from the game folder to check whether it works outside of Ascendara.
- Online-Fix games require Steam to be running and signed in. Ascendara offers to start Steam.
- VR games may need to be launched through SteamVR or the Meta Quest app. See [VR Games](/docs/troubleshooting/vr-games).

After you've played a game for more than 3 hours, Ascendara reminds you to support its developers by buying it. You can turn this off with **Developer Support Reminder** in **Settings**.

## The Game Page

Open a game from your Library to see its [game information](/docs/features/ascendara-xtra) and manage it.

### Executables

Select **Change or Add Executable** to open the executable manager. A game can have several executables (`.exe`, `.bat`, or `.cmd`), and the **primary** executable runs when you click **Play**. If Ascendara can't find the executable, look in subfolders such as `bin`, `x64`, `x86`, or `Binaries`.

### Launch Options

Enter launch arguments that are passed to the game executable. On Linux, when a game runs through Proton, UMU-Launcher, or Wine, arguments in the form `NAME=value` are set as environment variables and `%command%` is ignored, so Steam-style launch options generally work.

### Shortcuts

On Windows, create a desktop shortcut or add the game to the Start Menu. Shortcuts launch the game through the Game Handler, so playtime and Discord Rich Presence still work. Turn on **Automatically Create Shortcuts** in **Settings** to create a desktop shortcut when each download finishes.

### Other Actions

- **Save Backups**: Back up and restore saves. See [Game Backups](/docs/features/game-backups).
- **Verify Game Files**: Check the installed files.
- **Open Game Directory**
- **Edit Game Info** and **Edit Assets**: Fix the Steam match or change artwork.
- **Edit Game Entry**: Edit the game's raw data. Turn on **Extra Game Options** in **Settings** to show this option. Playtime, launch count, and rating status are protected; editing them can get your Ascend account hidden from the leaderboard.
- **Update**: Shown when a newer version is available in the index.
- **Mods** and **Trainers**: Nexus Mods and FLiNG trainers for supported games, with [Ascend](/docs/features/ascend). Installed trainers can launch automatically with the game (**Launch with Trainer**).
- **Delete Game** or **Remove from Library**: Deleting removes the game's files. Ascendara asks whether to keep the game's data in History.

On Linux, the game page also lets you set a UMU ID and reset the game's compatibility prefix. See [Linux and macOS](/docs/features/linux-and-macos).

### Game Profiles

Game Profiles keep a game's launch commands, automatic save backups, UMU ID, and custom save paths together in one place. Open them from the command palette (**Ctrl+K**) with **Manage Game Profiles**.

## Achievements

The [Achievement Watcher](/docs/binary-tool/achievement-watcher) runs in the background and detects achievements unlocked in supported games. Achievements appear on the game page with their unlock dates, and Ascendara shows a notification when you unlock one. Add extra folders to watch in **Settings** → **Achievement Watcher**.

## Profile and Levels

Your **Profile** shows your level, XP, total playtime, most played games, download history, and your PC's specs. You earn XP by playing games. Your profile is stored locally unless you sync it with Ascend. You can also keep private notes on your profile.

## Library Value

Select **Library Value** in the Library sidebar to estimate the retail value of your library using the [CheapShark](https://www.cheapshark.com) API.
