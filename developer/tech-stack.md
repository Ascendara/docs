# Technology Stack

A comprehensive breakdown of the technologies powering Ascendara.

## Core Framework

### Electron (v28+)
The desktop application shell providing:
- **Main Process**: Node.js runtime for system operations
- **Renderer Process**: Chromium for the React UI
- **IPC**: Secure communication between processes
- **Native APIs**: File system, shell, notifications, tray

### React 18
Modern React with:
- **Functional Components**: Hooks-based architecture
- **Context API**: Global state management
- **Suspense**: Lazy loading and code splitting
- **Concurrent Features**: Improved rendering performance

### Vite
Build tooling providing:
- **Dev Server**: Hot Module Replacement (HMR)
- **Build**: Optimized production bundles
- **ESBuild**: Fast TypeScript/JSX transformation

---

## Frontend Dependencies

### UI Framework
| Package | Version | Purpose |
|---------|---------|---------|
| `tailwindcss` | ^3.x | Utility-first CSS framework |
| `framer-motion` | ^10.x | Animation library |
| `lucide-react` | ^0.x | Icon system |
| `@radix-ui/*` | ^1.x | Accessible UI primitives |

### State & Data
| Package | Version | Purpose |
|---------|---------|---------|
| `react-router-dom` | ^6.x | Client-side routing |
| `fuse.js` | ^7.x | Fuzzy search |
| `axios` | ^1.x | HTTP client |

### Utilities
| Package | Version | Purpose |
|---------|---------|---------|
| `clsx` | ^2.x | Conditional classNames |
| `date-fns` | ^2.x | Date formatting |
| `react-markdown` | ^9.x | Markdown rendering |
| `react-syntax-highlighter` | ^15.x | Code highlighting |

---

## Backend Dependencies (Electron Main)

### Core
| Package | Version | Purpose |
|---------|---------|---------|
| `electron` | ^28.x | Desktop framework |
| `fs-extra` | ^11.x | Enhanced file operations |
| `axios` | ^1.x | HTTP requests |
| `dotenv` | ^16.x | Environment variables |

### Integrations
| Package | Version | Purpose |
|---------|---------|---------|
| `discord-rpc` | ^4.x | Discord Rich Presence |
| `node-machine-id` | ^1.x | Unique machine identification |
| `diskusage` | ^1.x | Drive space information |

### Security
| Package | Version | Purpose |
|---------|---------|---------|
| `crypto` | (built-in) | AES-256-CBC encryption |

---

## Python Tools

### Runtime
- **Python**: 3.8+ required
- **PyInstaller**: Bundling to standalone executables

### Common Dependencies
| Package | Purpose |
|---------|---------|
| `requests` | HTTP client |
| `psutil` | Process management |
| `aiohttp` | Async HTTP |
| `aiofiles` | Async file I/O |

### Tool-Specific
| Tool | Key Dependencies |
|------|------------------|
| Downloader | `pySmartDL`, `patool`, `rarfile` |
| GoFile Helper | `aiohttp`, `unrar` |
| Torrent Handler | `qbittorrentapi` |
| Game Handler | `psutil`, `pypresence` |
| Notification Helper | `PyQt6` |
| Translator | `requests`, `json` |

---

## Build & Development

### Build Tools
| Tool | Purpose |
|------|---------|
| `electron-builder` | Package Electron apps |
| `vite` | Frontend bundling |
| `pyinstaller` | Python to executable |
| `pkg` | Node.js to executable |

### Development
| Tool | Purpose |
|------|---------|
| `concurrently` | Run multiple processes |
| `wait-on` | Wait for resources |
| `eslint` | Code linting |
| `prettier` | Code formatting |

### Package Managers
Supported: `npm`, `yarn`, `pnpm`, `bun`

---

## Platform Support

### Windows
- **Target**: Windows 10/11 (x64)
- **Installer**: NSIS
- **Native**: Full feature support

### macOS
- **Target**: macOS 10.15+ (Intel & Apple Silicon)
- **Installer**: DMG
- **Limitations**: Wine required for Windows games

### Linux
- **Target**: Ubuntu 20.04+, Fedora 35+
- **Installer**: AppImage
- **Limitations**: Wine required for Windows games

---

## API & Services

### Ascendara API
- **Base URL**: `https://api.ascendara.app`
- **Format**: JSON
- **Auth**: API key + HMAC signatures for images

### External Services
| Service | Purpose |
|---------|---------|
| GoFile.io | File hosting downloads |
| IGDB/Twitch | Game metadata & covers |
| Discord | Rich Presence |
| qBittorrent | Torrent downloads |

---

## File Formats

### Configuration
| File | Format | Purpose |
|------|--------|---------|
| `ascendarasettings.json` | JSON | User settings |
| `timestamp.ascendara.json` | JSON | App state & history |
| `games.json` | JSON | Custom games list |

### Game Data
| File | Format | Purpose |
|------|--------|---------|
| `{game}.ascendara.json` | JSON | Game metadata |
| `filemap.ascendara.json` | JSON | File verification |
| `achievements.ascendara.json` | JSON | Achievement data |

### Themes
| File | Format | Purpose |
|------|--------|---------|
| `ascendaratheme.json` | JSON | Custom theme export |

---

## Security Considerations

### Encryption
- **Algorithm**: AES-256-CBC
- **Key Derivation**: Machine ID + salt
- **Usage**: Sensitive settings storage

### IPC Security
- **Context Isolation**: Enabled
- **Node Integration**: Disabled in renderer
- **Preload Scripts**: Whitelist-based API exposure

### API Security
- **Rate Limiting**: Per-endpoint limits
- **Image Signing**: HMAC-SHA256 signatures
- **HTTPS**: All API communication encrypted