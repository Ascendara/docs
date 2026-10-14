# Building Ascendara from Source

## Prerequisites

To run Ascendara in development mode:

- **Node.js** 22 (CI uses 22.14.0)
- **Yarn** 1.x (CI uses 1.22.22). npm also works for installing and running scripts.
- **Python** 3.12 with `pip`, for the helper tools
- **Git**, and a cloned repository. The Vite config reads the current commit with `git rev-parse HEAD`, so building from a ZIP download without Git history fails.
- **Windows**: Visual Studio Build Tools with the Windows SDK, for native dependencies

To build installers, you also need:

- **Rust and Cargo**, for the Crash Reporter
- **Windows**: [7-Zip](https://www.7-zip.org) x64 installed in `C:\Program Files\7-Zip` (it's bundled into the downloader)
- **Linux**: `unrar`, `p7zip-full`, `libfuse2`, and the FLTK/X11/OpenGL development packages (Debian/Ubuntu package names)
- **macOS**: Xcode Command Line Tools and Python 3 with `venv`

## Important Limitations

When you run Ascendara from source, it runs in **development mode**, and some features are restricted:

- **Game execution**: Games can't be launched in development mode. Build and run a packaged version to play games.
- **Ascend**: Ascend features are disabled unless you configure Firebase credentials (see below). The Ascend page shows a **Development Mode** notice.
- **Protected API features**: Official builds sign their requests to the Ascendara API with a private secret that isn't in the repository. Without it, protected endpoints such as game reports, ratings, feature reporting, analytics, and the [index relay](/docs/features/refreshing-index#helping-refresh-the-index) may reject requests. Public endpoints such as the game index keep working.
- **Build registration**: Official builds are registered with the Ascendara backend. Builds you make yourself aren't registered, so features that require an authorized build won't work in them.

These limitations protect Ascendara's infrastructure from abuse. The reporting and analytics services are essential for monitoring health, detecting issues, and keeping Ascendara secure, so they're restricted to official builds. You can still play games with the official release of Ascendara.

## Getting the Source Code

> **Note:** You can use any compatible package manager, such as `npm` or `yarn`.

1. Clone the repository:

```bash
git clone https://github.com/Ascendara/ascendara.git
cd ascendara
```

2. Install the Node dependencies:

```bash
yarn install
# or
npm install
```

3. Set up a Python virtual environment and install the Python dependencies:

```bash
python -m venv .venv
```

On Windows PowerShell, you may need to allow scripts for the current user first:

```ps1
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

Activate the environment and install the requirements:

```bash
# Windows
.venv\Scripts\activate
# macOS / Linux
source .venv/bin/activate

pip install -r requirements.txt
```

Each helper tool also has its own `requirements.txt` in `binaries/<tool>/` if you work on a specific tool.

### Configure Firebase (Optional)

Firebase powers Ascend accounts. It's optional for development. If you have your own Firebase project, copy the example environment file and fill in the `VITE_FIREBASE_*` values from the [Firebase Console](https://console.firebase.google.com/):

```bash
cp .env.example .env
```

Firebase initializes automatically when credentials are present. Leave the values empty to run without authentication features. The `.env` file is ignored by Git.

> **Warning:** Vite embeds every `VITE_*` value in the renderer bundle. Never put server credentials in these variables.

## Development Preview

Start the development app:

```bash
yarn start
# or
npm run start
```

This runs Vite and Electron together: Vite serves the renderer at `http://localhost:5173` with hot reloading, and Electron starts once the dev server is ready.

Other useful scripts:

| Command | Description |
| --- | --- |
| `yarn dev` | Run only the Vite dev server |
| `yarn build` | Build the renderer into `build/` |
| `yarn lint` | Run ESLint |
| `yarn format` | Format the code with Prettier |

## Building the Application

Each platform has its own build script. All of them build the renderer, fix its asset paths for Electron, generate a build signature, and package the app with electron-builder into `dist/`.

### Build Signature

Every build embeds a signature in `electron/build-signature.json`, generated with a `BUILD_SECRET`. Set one before building, either as an environment variable or in a `build.config.json` file in the project root (this file is ignored by Git):

```bash
# Generate a random secret
node -e "console.log(require('crypto').randomBytes(32).toString('base64'))"

# Linux / macOS
export BUILD_SECRET="your-secret-here"
# Windows (PowerShell)
$env:BUILD_SECRET="your-secret-here"
```

After packaging, the build scripts try to register the build with the Ascendara backend, which requires a private admin key. Pass `--skip-registration` to skip this step for your own builds.

### Windows

Build the helper executables first, then package the app:

```bash
python scripts/build_windows_helpers.py
yarn dist --skip-registration
```

`build_windows_helpers.py` must run on a native Windows x64 machine. It creates a virtual environment in `.build_venv`, builds the Downloader, Game Handler, Notification Helper, and Torrent Handler with PyInstaller, builds the Crash Reporter with Cargo, and builds the Achievement Watcher with `pkg`. See [Creating the Tools](/docs/developer/creating-the-tools).

The packaged app is created in `dist/`, with an NSIS installer and an unpacked version in `dist/win-unpacked/`.

### Linux

```bash
yarn dist-linux --skip-registration
```

The Linux script builds all helpers, the renderer, and an AppImage in `dist/`. If AppImage creation fails (for example, because of low disk space), it falls back to an unpacked build and a `.tar.gz` archive.

### macOS

```bash
yarn setup-mac                 # download the official 7-Zip for macOS
yarn dist-mac --check          # check prerequisites
yarn dist-mac --skip-registration
```

`yarn dist-mac` builds for the architecture of your Mac and creates a DMG and ZIP in `dist/`. Use `yarn dist-mac-arm64`, `yarn dist-mac-intel`, or `yarn dist-mac-all` to choose the architecture, and `--dir` to build only an unpacked `.app`. Building both architectures on Apple Silicon requires a universal2 Python, Rosetta 2, and both Rust targets. See `README_MAC_BUILD.md` in the repository for signing, notarization, and cross-architecture details.

### Manual Build Steps

The build scripts automate these steps:

1. Build the renderer: `yarn build`
2. In `build/index.html`, replace absolute `/assets/` paths with relative `assets/` paths.
3. Copy `build/index.html` and the `build/assets` directory into `electron/`.
4. Generate the build signature: `node scripts/generate_build_signature.js`
5. Package with electron-builder:
   ```bash
   yarn electron-builder --config.extraMetadata.main=electron/app.js --win --x64 --publish never
   ```
   Use `--linux` or `--mac` for other platforms. The helper binaries must already be built at the paths listed in `package.json` under `build.<platform>.extraResources`.
6. Remove the copied files from `electron/` (`index.html`, `assets/`).

## Automated Releases

Official releases are built by GitHub Actions for Windows x64, Linux x64, macOS arm64, and macOS Intel. The workflow and the required secrets are described in `docs/BUILD_AUTOMATION.md` in the repository. Contributors don't need to run it.
