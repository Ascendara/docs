# Technology Stack

A breakdown of the technologies that power Ascendara. Versions are the ranges in `package.json` and each tool's requirements file.

## Core Framework

### Electron (^40)
The desktop shell:
- **Main process**: Node.js runtime for system operations, child processes, and IPC
- **Renderer**: Chromium for the React UI, with context isolation and no Node integration
- **Packaging**: electron-builder (^26)

### React (^19)
- Functional components and hooks
- Context providers for global state
- React Router (^7) for navigation

### Vite (^7)
- Development server with hot module replacement on port 5173
- Production build into `build/`

---

## Frontend Dependencies

### UI
| Package | Version | Purpose |
|---------|---------|---------|
| `tailwindcss` | ^3.4 | Utility-first CSS |
| `@radix-ui/*` | ^1–2 | Accessible primitives behind the shadcn/ui components |
| `framer-motion` | ^12 | Animations |
| `lucide-react` | ^0.575 | Icons |
| `sonner` | ^2 | Toast notifications |
| `react-dnd` | ^16 | Drag and drop |
| `next-themes` | ^0.4 | Theme switching |

### State, Data, and i18n
| Package | Version | Purpose |
|---------|---------|---------|
| `react-router-dom` | ^7 | Routing |
| `zustand` | ^5 | Lightweight stores |
| `i18next`, `react-i18next` | ^25, ^16 | Translations |
| `firebase` | ^12 | Ascend accounts and cloud data |
| `axios` | ^1 | HTTP client |
| `dompurify` | ^3 | Sanitizing HTML from external sources |
| `cheerio` | ^1 | Parsing KHInsider soundtrack pages |

### Utilities
| Package | Version | Purpose |
|---------|---------|---------|
| `clsx`, `tailwind-merge`, `class-variance-authority` | — | Class name composition |
| `qrcode` | ^1.5 | Companion pairing QR codes |
| `jszip`, `unzipper` | — | ZIP handling (cloud save backups, dependency and catalogue archives) |

---

## Main Process Dependencies

| Package | Version | Purpose |
|---------|---------|---------|
| `fs-extra` | ^11 | File operations |
| `dotenv` | ^17 | Environment variables in development |
| `electron-dl` | ^4 | Sidecar downloads |
| `discord-rpc` | ^4 | Discord Rich Presence |
| `node-machine-id` | ^1 | Machine ID for settings encryption |
| `winreg` | ^1.2 | Windows registry checks (dependencies, launcher import) |
| `sax` | ^1.2 | Streaming XML parsing of LaunchBox metadata for Retro |
| `crypto` | built-in | AES-256-CBC encryption, HMAC request signing |

---

## Helper Tools

### Python Tools
- **Python**: 3.12 in CI
- **PyInstaller**: 6.16.0, building single-file executables

| Tool | Key dependencies |
|------|------------------|
| Downloader | `requests`, `patool`, `beautifulsoup4`, `unrar` (Windows), plus bundled 7-Zip and UnRAR |
| Game Handler | `psutil`, `pypresence` |
| Torrent Handler | `qbittorrent-api` |
| Notification Helper | `PyQt6`, `requests` |
| Language Translation | `requests` |
| Local Refresh | `cloudscraper`, `requests`, `beautifulsoup4` |

### Crash Reporter (Rust)
- **Rust** 2021 edition, built with Cargo
- `fltk` (bundled FLTK) and `fltk-theme` for the window, `webbrowser`, `chrono`, and `winres` to embed the Windows icon

### Achievement Watcher (Node.js)
- Packaged with `pkg` 5.8 on a Node 18 runtime
- `node-watch`, `single-instance`, `moment`, `ini`, `regedit`, `find-up`

### External Tools
| Tool | Purpose |
|------|---------|
| [Ludusavi](https://github.com/mtkennerly/ludusavi) | Save backups |
| SteamCMD | Workshop Downloader |
| qBittorrent (Web UI) | Torrent downloads |
| Proton-GE, Proton CachyOS, UMU-Launcher, Wine | Running Windows games on Linux and macOS |
| 7-Zip, UnRAR | Archive extraction (bundled into the downloader) |

---

## Build & Development

### Build Tools
| Tool | Purpose |
|------|---------|
| `electron-builder` | Packaging (NSIS, DMG/ZIP, AppImage) |
| `vite` | Renderer bundling |
| `pyinstaller` | Python helpers |
| `cargo` | Crash Reporter |
| `pkg` | Achievement Watcher |

### Development
| Tool | Purpose |
|------|---------|
| `concurrently`, `wait-on` | Run Vite and Electron together in `yarn start` |
| `eslint` (^9) | Linting |
| `prettier` (^3) | Formatting, with Tailwind and JSON-sorting plugins |
| `husky`, `lint-staged` | Pre-commit formatting |

### Package Managers
Yarn 1.x is used by the build scripts and CI. npm also works for installing dependencies and running scripts.

---

## Platform Support

### Windows
- **Target**: Windows 10/11 (x64)
- **Installer**: NSIS
- **Support**: Full

### Linux
- **Target**: x64
- **Package**: AppImage (installed with `install.sh`)
- **Windows games**: UMU-Launcher, Proton, or Wine

### macOS
- **Target**: Apple Silicon (arm64) and Intel (x64), as separate builds
- **Package**: DMG and ZIP
- **Windows games**: Wine. Some features are Windows-only.

---

## Services

### Ascendara API
- **Base URL**: `https://api.ascendara.app`
- **Format**: JSON
- **Auth**: Public endpoints need no authentication. Protected endpoints use rotating HMAC headers and a build signature from official builds.

See the [API Reference](/docs/api-reference/overview).

### Other Ascendara Services
| Host | Purpose |
|------|---------|
| `cdn.ascendara.app` | Installer, sidecars, dependency packages |
| `lfs.ascendara.app` | Update downloads |
| `r2.ascendara.app` | Game index covers |
| `monitor.ascendara.app` | Ascendara Companion and download sync |
| `webview.ascendara.app` | Ascendara Companion web app |

### External Services
| Service | Purpose |
|---------|---------|
| Firebase | Ascend authentication and data |
| Stripe | Ascend checkout |
| Steam (through the Ascendara API) | Game information and achievements |
| SteamGridDB (through the Ascendara API) | Artwork |
| HowLongToBeat | Time to beat |
| KHInsider | Soundtracks |
| CheapShark | Library value |
| Nexus Mods, FLiNG | Mods and trainers |
| TorBox | Debrid downloads |
| Discord | Rich Presence |

---

## File Formats

### Configuration
| File | Purpose |
|------|---------|
| `ascendarasettings.json` | User settings |
| `timestamp.ascendara.json` | App state |
| `games.json` | Custom and imported games (in the download directory) |

### Game Data
| File | Purpose |
|------|---------|
| `{game}.ascendara.json` | Game metadata and download state |
| `filemap.ascendara.json` | Installed-file manifest for verification |
| `achievements.ascendara.json` | Achievement data |

### Game Index
| File | Purpose |
|------|---------|
| `ascendara_games.json` | The game list |
| `sync.json`, `progress.json` | Sync state |

### Themes
| File | Purpose |
|------|---------|
| `ascendaratheme.json` | Exported custom theme |

---

## Security Considerations

### Encryption
- **Algorithm**: AES-256-CBC
- **Key**: SHA-256 of the machine ID
- **Usage**: Sensitive settings such as the TorBox API key

### IPC Security
- **Context isolation**: Enabled
- **Node integration**: Disabled in the renderer
- **IPC guard**: Handlers only accept calls from Ascendara's own window
- **Preload API**: Only the methods in `preload.js` are exposed

### API Security
- **Rate limiting**: Per-endpoint limits
- **Signed requests**: HMAC-based headers for protected endpoints
- **HTTPS**: All API communication is encrypted
