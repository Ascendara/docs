# Creating the Tools for Production Builds

Ascendara's helper tools are bundled into standalone executables so users don't need Python, Node.js, or any dependencies installed. The Downloader, Game Handler, and Crash Reporter are written in Rust and built with Cargo, the Python tools are built with PyInstaller (6.16.0), and the Achievement Watcher is built with `pkg`.

Each platform has a build script that builds the tools for you. You normally don't need to run PyInstaller yourself.

## Which Tools Each Platform Ships

| Tool | Windows | Linux | macOS |
| --- | --- | --- | --- |
| AscendaraDownloader (Rust) | Bundled | Bundled | Bundled |
| AscendaraGameHandler (Rust) | Bundled | Bundled | Bundled |
| AscendaraNotificationHelper | Bundled | Bundled | Bundled |
| AscendaraCrashReporter (Rust) | Bundled | Bundled | Bundled |
| AscendaraAchievementWatcher (Node.js) | Bundled | Bundled | Bundled |
| AscendaraLanguageTranslation | Downloaded on demand | Bundled | Bundled |
| Highball | — | — | Bundled (Apple Silicon only) |
| Ludusavi | Downloaded on demand | Downloaded on demand | Downloaded on demand, or found on `PATH` |

On-demand tools ("sidecars") are downloaded from `cdn.ascendara.app` the first time a feature needs them. See [System Design](/docs/developer/system-design#sidecars).

## Windows

```bash
python scripts/build_windows_helpers.py
```

Run this on a native Windows x64 machine with Cargo and Yarn installed. The script:

1. Creates a virtual environment in `.build_venv` and installs PyInstaller and the Notification Helper's requirements.
2. Builds the Notification Helper as a single windowed executable (so Windows doesn't open a console window when Ascendara starts it). It bundles its `sounds` folder and adds `--hidden-import PyQt6.QtMultimedia` so it can play notification sounds. Language Translation isn't built here; on Windows it's downloaded on demand.
3. Builds the Rust tools: `cargo build --release --locked` in `binaries/AscendaraCrashReporter`, `binaries/AscendaraGameHandler`, and `binaries/AscendaraDownloader`. The Downloader extracts archives itself, so it needs no bundled 7-Zip or UnRAR.
4. Builds the Achievement Watcher: `yarn install --frozen-lockfile` and `yarn build-win` in `binaries/AscendaraAchievementWatcher`.
5. Checks that every resource listed in `package.json` exists and is a Windows x64 executable.

The outputs are placed where `package.json` expects them:

| Tool | Output |
| --- | --- |
| Python tools | `binaries/<Tool>/dist/<Tool>.exe` |
| Crash Reporter | `binaries/AscendaraCrashReporter/target/release/AscendaraCrashReporter.exe` |
| Game Handler | `binaries/AscendaraGameHandler/target/release/AscendaraGameHandler.exe` |
| Downloader | `binaries/AscendaraDownloader/target/release/AscendaraDownloader.exe` |
| Achievement Watcher | `binaries/AscendaraAchievementWatcher/dist/AscendaraAchievementWatcher.exe` |

## Linux

`yarn dist-linux` builds the tools as part of the app build:

- Creates `.build_venv`, installs PyInstaller and the tools' requirements, and builds Language Translation and the Notification Helper as single-file executables.
- Builds the Downloader, Crash Reporter, and Game Handler with Cargo and the Achievement Watcher with `yarn build-linux`.

## macOS

`yarn dist-mac` builds the tools for each target architecture in its own virtual environment (`.build_venv_mac/<arch>`), and builds the Downloader, Game Handler, and Crash Reporter with an explicit Rust target. No 7-Zip or UnRAR setup is needed: the Downloader extracts ZIP, 7z, and RAR archives itself. Apple Silicon builds also build the pinned Highball CLI (`yarn setup-highball`, or `scripts/build_highball.py`) and bundle it in `Contents/Resources/Highball` with its engine manifest, compatibility data, and license notices. Intel builds don't include Highball and use the system Wine. See `README_MAC_BUILD.md` in the repository.

## Achievement Watcher

> **Note:** This tool is written in JavaScript, not Python. It's packaged with `pkg` 5.8 using a Node 18 runtime.

```bash
cd binaries/AscendaraAchievementWatcher
yarn install --frozen-lockfile
yarn build-win     # or build-linux / build-mac
```

The output is `dist/AscendaraAchievementWatcher(.exe)`.

## Downloader, Game Handler, and Crash Reporter

> **Note:** These tools are written in Rust and need Rust 1.90 or newer. The Crash Reporter uses the FLTK GUI toolkit.

```bash
cd binaries/AscendaraDownloader   # or AscendaraGameHandler / AscendaraCrashReporter
cargo build --release --locked
```

The output is `target/release/<Tool>(.exe)`. On Windows, `build.rs` embeds the icon into the executable.

## Building a Single Tool Manually

To build one Python tool by hand, install its requirements and run PyInstaller from the project root:

```bash
pip install -r binaries/AscendaraNotificationHelper/requirements.txt pyinstaller==6.16.0
python -m PyInstaller --onefile --noconfirm --clean --windowed \
  --name AscendaraNotificationHelper \
  --icon binaries/AscendaraNotificationHelper/src/ascendara.ico \
  --distpath binaries/AscendaraNotificationHelper/dist \
  --add-data "binaries/AscendaraNotificationHelper/src/sounds;sounds" \
  --hidden-import PyQt6.QtMultimedia \
  binaries/AscendaraNotificationHelper/src/AscendaraNotificationHelper.py
```

On Linux and macOS, use `:` instead of `;` to separate the source and destination in `--add-data` and `--add-binary`.

## Command Options Explained

- `--onefile`: Bundles everything into a single executable
- `--windowed`: Builds a GUI-subsystem executable, so no console window opens when Ascendara starts the tool
- `--icon`: Sets the executable's icon
- `--add-binary`: Includes native binaries such as `7z.exe`
- `--add-data`: Includes data files such as the icon and license notices
- `--hidden-import`: Includes modules PyInstaller can't detect automatically
- `--distpath`: Where the executable is written

## Running Tools From Source

In development mode, Ascendara starts the Python tools from `binaries/<Tool>/dist/` and the Rust tools from `binaries/<Tool>/target/release/`, so build the tools you're testing. You can also run a tool directly, for example:

```bash
cd binaries/AscendaraDownloader
cargo run --release -- "https://example.com/game.zip" "Example Game" false false false false "1.0" "1 GB" ./games
```
