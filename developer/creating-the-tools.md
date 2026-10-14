# Creating the Tools for Production Builds

Ascendara's helper tools are bundled into standalone executables so users don't need Python, Node.js, or any dependencies installed. The Python tools are built with PyInstaller (6.16.0), the Crash Reporter with Cargo, and the Achievement Watcher with `pkg`.

Each platform has a build script that builds the tools for you. You normally don't need to run PyInstaller yourself.

## Which Tools Each Platform Ships

| Tool | Windows | Linux | macOS |
| --- | --- | --- | --- |
| AscendaraDownloader | Bundled | Bundled | Bundled |
| AscendaraGameHandler | Bundled | Bundled | Bundled |
| AscendaraNotificationHelper | Bundled | Bundled | Bundled |
| AscendaraTorrentHandler | Bundled | Bundled | Bundled |
| AscendaraCrashReporter (Rust) | Bundled | Bundled | Bundled |
| AscendaraAchievementWatcher (Node.js) | Bundled | Bundled | Bundled |
| AscendaraLanguageTranslation | Downloaded on demand | Bundled | Bundled |
| AscendaraLocalRefresh | Not shipped | Not shipped | Not shipped |
| Ludusavi | Downloaded on demand | Downloaded on demand | Downloaded on demand, or found on `PATH` |

On-demand tools ("sidecars") are downloaded from `cdn.ascendara.app` the first time a feature needs them. See [System Design](/docs/developer/system-design#sidecars).

## Windows

```bash
python scripts/build_windows_helpers.py
```

Run this on a native Windows x64 machine with Cargo, Yarn, and 7-Zip x64 installed in `C:\Program Files\7-Zip`. The script:

1. Creates a virtual environment in `.build_venv` and installs PyInstaller and each tool's `requirements.txt`.
2. Builds each Python tool as a single windowed executable (so Windows doesn't open a console window when Ascendara starts it):
   ```bash
   python -m PyInstaller --onefile --noconfirm --clean --windowed \
     --name AscendaraDownloader \
     --icon binaries/AscendaraDownloader/src/ascendara.ico \
     --distpath binaries/AscendaraDownloader/dist \
     --add-data "binaries/AscendaraDownloader/src/ascendara.ico;." \
     --add-binary "C:\Program Files\7-Zip\7z.exe;." \
     --add-binary "C:\Program Files\7-Zip\7z.dll;." \
     --add-data "C:\Program Files\7-Zip\License.txt;licenses/7zip" \
     --add-binary "binaries/AscendaraDownloader/src/UnRAR.dll;." \
     --hidden-import unrar.unrarlib \
     binaries/AscendaraDownloader/src/AscendaraDownloader.py
   ```
   The Downloader and Torrent Handler bundle 7-Zip and its license. The Downloader also bundles `UnRAR.dll`. The Game Handler and Notification Helper use the same command without the extra binaries.
3. Builds the Crash Reporter: `cargo build --release --locked` in `binaries/AscendaraCrashReporter`.
4. Builds the Achievement Watcher: `yarn install --frozen-lockfile` and `yarn build-win` in `binaries/AscendaraAchievementWatcher`.
5. Checks that every resource listed in `package.json` exists and is a Windows x64 executable.

The outputs are placed where `package.json` expects them:

| Tool | Output |
| --- | --- |
| Python tools | `binaries/<Tool>/dist/<Tool>.exe` |
| Crash Reporter | `binaries/AscendaraCrashReporter/target/release/AscendaraCrashReporter.exe` |
| Achievement Watcher | `binaries/AscendaraAchievementWatcher/dist/AscendaraAchievementWatcher.exe` |

## Linux

`yarn dist-linux` builds the tools as part of the app build:

- Creates `.build_venv`, installs PyInstaller and every tool's requirements, and builds the Downloader, Game Handler, Language Translation, Torrent Handler, and Notification Helper as single-file executables.
- Bundles the system `unrar` and p7zip's `7z` and `7z.so` (with their license notices) into the Downloader and Torrent Handler. The script uses Debian/Ubuntu paths; other distributions may need adjustments.
- Builds the Crash Reporter with Cargo and the Achievement Watcher with `yarn build-linux`.

## macOS

`yarn dist-mac` builds the tools for each target architecture in its own virtual environment (`.build_venv_mac/<arch>`), passes an explicit Rust target to Cargo, and bundles the official macOS `7zz` (downloaded by `yarn setup-mac`) into the Downloader and Torrent Handler. See `README_MAC_BUILD.md` in the repository.

## Achievement Watcher

> **Note:** This tool is written in JavaScript, not Python. It's packaged with `pkg` 5.8 using a Node 18 runtime.

```bash
cd binaries/AscendaraAchievementWatcher
yarn install --frozen-lockfile
yarn build-win     # or build-linux / build-mac
```

The output is `dist/AscendaraAchievementWatcher(.exe)`.

## Crash Reporter

> **Note:** This tool is written in Rust with the FLTK GUI toolkit.

```bash
cd binaries/AscendaraCrashReporter
cargo build --release --locked
```

The output is `target/release/AscendaraCrashReporter(.exe)`. On Windows, `build.rs` embeds the icon into the executable.

## Building a Single Tool Manually

To build one Python tool by hand, install its requirements and run PyInstaller from the project root:

```bash
pip install -r binaries/AscendaraGameHandler/requirements.txt pyinstaller==6.16.0
python -m PyInstaller --onefile --noconfirm --clean --windowed \
  --name AscendaraGameHandler \
  --icon binaries/AscendaraGameHandler/src/ascendara.ico \
  --distpath binaries/AscendaraGameHandler/dist \
  binaries/AscendaraGameHandler/src/AscendaraGameHandler.py
```

On Linux and macOS, use `:` instead of `;` to separate the source and destination in `--add-data` and `--add-binary`.

## Command Options Explained

- `--onefile`: Bundles everything into a single executable
- `--windowed`: Builds a GUI-subsystem executable, so no console window opens when Ascendara starts the tool
- `--icon`: Sets the executable's icon
- `--add-binary`: Includes native binaries such as `7z.exe` or `UnRAR.dll`
- `--add-data`: Includes data files such as the icon and license notices
- `--hidden-import`: Includes modules PyInstaller can't detect automatically
- `--distpath`: Where the executable is written

## Running Tools From Source

In development mode, Ascendara starts some tools from their Python source (for example, the Game Handler) and others from `binaries/<Tool>/dist/`, so build the tools you're testing. You can also run any Python tool directly:

```bash
python binaries/AscendaraDownloader/src/AscendaraDownloader.py --help
```
