# Webview Development

The Ascendara Webview (the Companion) is a Next.js web app that pairs with the Ascendara desktop app through a six-digit code. Once connected it lets you browse the game index, view and launch your installed desktop games, check your synced cloud collection, follow and control downloads, see friends' presence, and manage the connection, all from a phone or another browser. This document covers the architecture, the desktop integration, and the development workflow.

## Architecture Overview

The webview never talks to the desktop app directly. Both sides talk to the monitor API (`monitor.ascendara.app`), which stores what the desktop reports and queues commands the webview sends back:

```
┌──────────────────────┐      ┌──────────────────────────┐      ┌──────────────────────┐
│  Desktop App         │      │  Monitor API             │      │  Webview (Next.js)   │
│                      │      │                          │      │                      │
│  • Connection codes  │─────►│  • Sessions & devices    │◄─────│  • Discover / games  │
│  • Download sync     │      │  • Download data & queue │      │  • Library & launch  │
│  • Library sync      │◄─────│  • Library data & queue  │─────►│  • Cloud collection  │
│  • Command handling  │      │  • Game index & artwork  │      │  • Downloads         │
│                      │      │  • Firebase account data │      │  • Friends / Profile │
└──────────────────────┘      └──────────────────────────┘      └──────────────────────┘
   Firebase ID token                                               X-Session-ID header
```

Behind the monitor:

```text
Monitor API
  -> Firebase (accounts, devices, friends, cloudLibrary, profileStats)
  -> Ascendara game index (https://api.ascendara.app/json/games)
  -> Ascendara SteamGrid proxy -> artwork URLs
```

The desktop authenticates with the signed-in user's Firebase ID token. The webview only ever holds a monitor session ID, which it sends as the `X-Session-ID` header. The monitor resolves the account owner from that session and scopes every read to it.

### Connection Flow

```
User generates a connection code in the desktop app (Ascend → Companion)
    │
    ▼
User scans the QR code (webview.ascendara.app/?code=CODE),
opens webview.ascendara.app/CODE, or types the code in
    │
    ▼
Webview sends POST /verify-code
    │
    ▼
Monitor verifies the code and returns sessionId, userId and displayName
    │
    ▼
Webview stores the session in localStorage and opens /library
```

`/<code>` is handled by `app/[code]/page.tsx`, which redirects any six-digit path to `/?code=<code>`. If a saved session already exists, visiting `/` goes straight to `/library` unless the URL has `?reconnect` or a valid `code` parameter.

---

## Desktop App Integration

The desktop side lives in the main Ascendara repository. Everything is sent to `monitor.ascendara.app` with the user's Firebase ID token.

### Connection and downloads

| Step | Endpoint | Where |
| --- | --- | --- |
| Generate a connection code | `POST /generate-code` | `src/pages/ascend/useAscendPage.js` |
| Wait for the device to connect | `GET /connection-status/{code}` | `src/pages/ascend/useAscendPage.js` |
| List or disconnect devices | `GET /connected-devices/{uid}`, `POST /disconnect-device` | `src/pages/ascend/useAscendPage.js` |
| Push download progress | `POST /downloads/sync` (every 5 seconds while downloads are active) | `src/services/downloadSyncService.js` |
| Notify that a download started | `POST /downloads/notify-start` | `src/services/downloadSyncService.js` |
| Fetch remote download commands | `GET /downloads/commands/{uid}` | `src/services/downloadSyncService.js` |
| Acknowledge a download command | `POST /downloads/commands/acknowledge` | `src/services/downloadSyncService.js` |

The connection code's lifetime comes from the backend (`expiresIn`, 300 seconds by default). The QR code is generated in the main process by `electron/modules/qrcode.js`. The Downloads page executes `pause`, `resume`, and `stop`/`cancel`/`kill` commands and acknowledges each one as `completed` or `failed`.

### Installed library and remote launch

`src/services/desktopLibrarySyncService.js` keeps the Companion's Library view current and runs its command queue. `src/app.jsx` starts it with `initializeDesktopLibrarySync(user)` when the user signs in and stops it on sign-out. It ticks every 3 seconds:

| Step | Endpoint | Frequency |
| --- | --- | --- |
| Send the installed library | `POST /library/sync` | Every 15 seconds (retries 15 seconds after a failure) |
| Report running game sessions | `POST /library/activity` | Every tick |
| Fetch queued commands | `GET /library/commands/{uid}?actions=launch,stop` | Every tick |
| Claim a command | `POST /library/commands/claim` | Once per command |
| Report the result | `POST /library/commands/acknowledge` | Once per command, retried until it succeeds |

The library snapshot includes installed and custom games, but leaves out deleted games and games that are still downloading or extracting. Each game gets an opaque ID: the first 16 bytes of a SHA-256 hash of its name, source folder and (for custom games) executable. The snapshot carries display metadata only: name, version, playtime, launch count, last played, favorite, description, size, categories, and online/DLC flags. Executable paths never leave the desktop.

When a command arrives, the desktop claims it first. If the claim fails, another signed-in desktop has already taken it. The desktop then looks the game ID up in its current local list and handles the action:

- **`launch`**: refused in developer mode, when the game is already running, for an online game that has never been launched in Ascendara, for an online game on Windows when Steam is not running, and for VR games. Otherwise the game launches through `window.electron.playGame` and is added to recent games.
- **`stop`**: calls `window.electron.stopGame(name, sessionId)` for the matching running session.

The outcome is acknowledged as `completed` or `failed` with an error message, which the webview shows to the user. Handled command IDs are remembered for 5 minutes so a command is never run twice.

---

## Webview Features

| Route | View | Data source |
| --- | --- | --- |
| `/` | Connection screen (code input, monitor status) | `POST /verify-code` |
| `/explore` | **Discover**: search the index, filter by genre, sort by recently updated, popular, or name | `GET /companion/games` |
| `/games/[id]` | Game details: artwork, description, system requirements, size, version, online/DLC | `GET /companion/games/{id}` |
| `/library` | **Library**: installed desktop games, "Jump back in" rail, desktop online/offline status, **Play on desktop** | `GET /companion/library`, `POST /companion/library/launch` |
| `/cloud-library` | Cloud collection: synced games with favorites/completed filters, playtime and achievements | `GET /companion/account` |
| `/dashboard` | **Downloads**: progress, speed, ETA, extraction status, pause/resume/stop | `GET /downloads/{userId}`, `POST /downloads/command` |
| `/friends` | **Friends**: presence grouped into active and offline, filterable by status | `POST /get-friends` |
| `/profile` | **Profile**: account info and level, theme picker, desktop connection, disconnect, version info | `GET /companion/account`, `POST /disconnect-device` |

On desktop the app uses a sidebar, which also has shortcuts to the cloud collection and to **Appearance & connection** (`/profile#settings`). On mobile it uses bottom navigation. The five main sections are Discover, Library, Downloads, Friends and Profile.

The webview shows information and sends remote commands only. Games are installed and run on the desktop, and the webview cannot edit the cloud library.

---

## Tech Stack

| Component | Technology |
|-----------|-----------|
| Framework | Next.js 16 (App Router) |
| UI | React 19 |
| Language | TypeScript |
| Styling | Tailwind CSS v4 plus shared classes in `app/globals.css` |
| Primitives | Radix UI (alert dialog, avatar, progress, slot), shadcn-style components in `components/ui/` |
| Icons | Lucide React |
| Installability | Web app manifest (`app/manifest.ts`) and a custom install prompt |

Node.js 20.9 or newer is required.

---

## Project Structure

```
webview/
├── app/
│   ├── page.tsx                 # Connection screen
│   ├── [code]/page.tsx          # Six-digit connection-link redirect
│   ├── explore/page.tsx         # Discover (catalog search)
│   ├── games/[id]/page.tsx      # Game details
│   ├── library/page.tsx         # Installed desktop library and remote launch
│   ├── cloud-library/page.tsx   # Synced cloud collection
│   ├── dashboard/page.tsx       # Download monitoring and controls
│   ├── friends/page.tsx         # Friends and presence
│   ├── profile/page.tsx         # Profile, appearance, connection, about
│   ├── layout.tsx               # Root layout, theme bootstrap script
│   ├── globals.css              # Layout and theme palettes
│   └── manifest.ts              # Web app manifest
├── components/
│   ├── companion-layout.tsx     # Page shell and shared UI (LoadState, Stat, SearchField, Pagination, GameArtwork…)
│   ├── bottom-navbar.tsx        # Desktop sidebar and mobile navigation
│   ├── connection-guard.tsx     # "Connection lost" screen for the downloads view
│   ├── download-card.tsx        # Download progress and controls
│   ├── mock-dashboard.tsx       # Development mock dashboard
│   ├── theme-selector.tsx       # Theme swatches
│   └── ui/                      # Base components (button, card, alert-dialog, menu-select, toast…)
├── contexts/
│   └── theme-context.tsx        # Theme selection and persistence
├── hooks/
│   ├── use-companion.ts         # Cached, de-duplicated /companion/* loading
│   ├── use-game-artwork.ts      # Batched artwork requests and persistent cache
│   ├── use-visible-games.ts     # Limits artwork requests to tiles near the viewport
│   └── use-mock-dashboard.ts    # Mock download data
├── lib/
│   ├── api.ts                   # Session and legacy monitor client (downloads, friends, disconnect)
│   ├── companion.ts             # /companion/* client and data types
│   ├── config.ts                # API URL and download polling interval
│   ├── connection-state.ts      # Shared download/friends/username cache for the dashboard
│   ├── crypto.ts                # Decrypts E2E-encrypted legacy responses
│   ├── dev-mode.ts              # Development mode check and mock code
│   ├── themes.ts                # Theme definitions
│   └── version.ts               # Monitor/API version shown on Profile
└── readme/
    └── companion-deployment.md  # Monitor rollout notes
```

### Key Files

#### `lib/companion.ts`

Client for the newer `/companion/*` routes. These return plain JSON over HTTPS, authenticated with the session header:

```typescript
companionFetch<T>(path: string, signal?: AbortSignal): Promise<T>  // GET /companion/{path}
companionPost<T>(path: string, body: unknown): Promise<T>          // POST /companion/{path}, 20 s timeout
```

Both throw a `CompanionError` that carries the HTTP `status`. A missing session throws with status `401`, which views show as a "Connect to Ascendara" prompt. The file also defines the `Game`, `Catalog`, `DesktopGame`, `DesktopLibrary`, `LibraryGame` and `Account` types, and the `playtime()` formatter.

#### `lib/api.ts`

The `apiClient` singleton manages the session and the older monitor routes:

```typescript
apiClient.verifyCode(code)       // POST /verify-code, stores session and user ID
apiClient.getDownloads()         // GET /downloads/{userId}
apiClient.pauseDownload(id)      // POST /downloads/command { command: 'pause' }
apiClient.resumeDownload(id)     // POST /downloads/command { command: 'resume' }
apiClient.cancelDownload(id)     // POST /downloads/command { command: 'kill' }
apiClient.getUserName()          // GET /getusername
apiClient.getFriends()           // POST /get-friends
apiClient.disconnect()           // POST /disconnect-device, then clears the local session
apiClient.getSessionId() / clearSession()
```

When a response is marked `e2e_encrypted`, it is decrypted with `decryptE2EData` from `lib/crypto.ts`, which uses AES-GCM with a key derived from the user ID. Plain JSON responses are accepted as they are.

#### `hooks/use-companion.ts`

`useCompanion<T>(path)` returns `{ data, error, disconnected, loading, retry }`. Responses are cached per session and path: catalog routes for 60 seconds, everything else for 15 seconds. Concurrent requests for the same key share one fetch. A catalog page also fills the game-detail cache, so opening a result does not need another request.

#### `hooks/use-game-artwork.ts`

`useGameArtwork(names)` returns a `name → url | null` map. Covers are fetched from `/companion/artwork?q=…` in batches of 6, with only one request in flight at a time. Results are cached in `localStorage` (`ascendara-artwork-cache`): hits for 7 days and misses for 6 hours. Names the monitor reports as `pending` are retried with backoff, and the hook retries unresolved names every 60 seconds while the view stays open. `useVisibleGames` limits requests to tiles within 300px of the viewport.

---

## Backend Communication

### Routes used by the webview

| Method | Route | Purpose |
| --- | --- | --- |
| `POST` | `/verify-code` | Exchange a connection code for a session |
| `GET` | `/getusername` | Connected user's display name |
| `GET` | `/downloads/{userId}` | Desktop download activity |
| `POST` | `/downloads/command` | Queue `pause`, `resume`, or `kill` for a download |
| `POST` | `/get-friends` | Friends and presence |
| `POST` | `/disconnect-device` | Unlink this browser |
| `GET` | `/companion/games` | Search and paginate the game index (`q`, `category`, `sort`, `page`) |
| `GET` | `/companion/games/{id}` | One index entry |
| `GET` | `/companion/library` | Installed desktop library, `online` flag and `lastSeen` |
| `POST` | `/companion/library/launch` | Queue a launch (`{ gameId }`), returns `commandId` |
| `GET` | `/companion/library/commands/{commandId}` | Launch status: `completed`, `failed`, or `expired` |
| `GET` | `/companion/account` | Profile and cloud-library data |
| `GET` | `/companion/artwork` | Resolve artwork for game names |

Companion routes check the connected device and scope reads to the session owner. They never accept a client-supplied UID. Catalog responses contain metadata only, never download links.

### Polling and freshness

| What | Interval |
| --- | --- |
| Downloads (`/dashboard`) | `config.pollingInterval` in `lib/config.ts` (3 seconds) |
| Installed library (`/library`) | 15 seconds |
| Launch status | Every 2 seconds, up to 30 attempts |
| Game index (monitor side) | Refreshed every 5 minutes; when the upstream is down, the last good index is served for up to an hour and marked `stale` |

The monitor marks the desktop offline when its last library sync is more than 45 seconds old. Play is disabled while it is offline. Pending launch commands expire after 90 seconds.

### Security

- The browser stores only the monitor session ID. Signing secrets, SteamGridDB keys, build credentials and the Firebase service account all stay on the monitor server, never in `NEXT_PUBLIC_*` variables.
- Legacy routes may return E2E-encrypted payloads, but the `/companion/*` routes are plain JSON over HTTPS. Do not describe the whole app as end-to-end encrypted.
- The desktop library sync never sends executable paths, and commands can only refer to IDs from a recent desktop snapshot.

---

## Development Workflow

### Setup

```bash
git clone https://github.com/ascendara/webview.git
cd webview
npm install
npm run dev
```

Open [localhost:3000](http://localhost:3000). The API URL comes from `NEXT_PUBLIC_API_BASE_URL` and defaults to `https://monitor.ascendara.app`. To point at a local monitor, create `.env.local`:

```dotenv
NEXT_PUBLIC_API_BASE_URL=http://localhost:54303
```

On a phone, `localhost` refers to the phone itself, so use the server's LAN address. Make sure the monitor's CORS allowlist includes the webapp's origin and the `Content-Type` and `X-Session-ID` headers.

All account-backed views need a real connection code from the desktop app.

### Mock Mode

For download UI work without a backend:

1. Run `npm run dev`
2. Enter code `123456` on the connection screen
3. The simulated download dashboard opens

Mock mode sets `mock_mode` in `localStorage`, which makes `apiClient` refuse any real request. It includes sample downloads with live progress and local pause/resume/remove actions. Mock mode only works when `NODE_ENV === 'development'`. It covers only the downloads dashboard: Discover, Library, Friends and Profile have no mock data. Leave mock mode through **Profile → Disconnect**.

### Checks

```bash
npx tsc --noEmit
npm run lint
npm run build
```

Before releasing, pair a real device and check the catalog, artwork, installed library and remote launch, cloud collection, friends, disconnecting, and download commands against the target monitor.

---

## Component Development

Pages are wrapped in `CompanionLayout`, which provides the navigation and page header. Shared building blocks come from the same file:

```tsx
import { CompanionLayout, LoadState, RefreshButton } from '@/components/companion-layout'
import { useCompanion } from '@/hooks/use-companion'
import type { Account } from '@/lib/companion'

export default function Example() {
  const state = useCompanion<Account>('account')
  return <CompanionLayout title="Example" eyebrow="Section"
    actions={<RefreshButton busy={state.loading} onClick={state.retry} />}>
    <LoadState {...state} />
    {state.data && <p>{state.data.profile.displayName}</p>}
  </CompanionLayout>
}
```

`LoadState` renders skeletons (`grid`, `list` or `detail` variants), errors with a retry button, or a reconnect prompt when `disconnected` is set. To show game covers, put `data-game-name` on each tile, attach `useVisibleGames`'s `gridRef` to the grid, and pass the visible names to `useGameArtwork`.

---

## Theme System

There are 12 themes in `lib/themes.ts`, split into two groups:

| Light | Dark |
| --- | --- |
| `light` (Arctic Sky), `blue` (Ocean Blue), `purple` (Ascendara Purple), `emerald`, `rose`, `amber` (Amber Sand) | `dark` (Dark Blue), `midnight`, `cyberpunk`, `sunset`, `forest`, `ocean` (Deep Ocean) |

The selected theme is saved in `localStorage` under `ascendara-theme`. If nothing is saved, the app follows the system color scheme. `ThemeProvider` sets `data-theme` and the `dark` class on `<html>`. The palettes themselves are defined in `app/globals.css`. An inline script in `app/layout.tsx` applies the saved theme before hydration to avoid a flash of the wrong theme.

```typescript
import { useTheme } from '@/contexts/theme-context'

const { theme, setTheme, isDark } = useTheme()
setTheme('midnight')
```

To add a theme, add it to `themes` (and `getThemeColors`) in `lib/themes.ts`, define its palette under its `data-theme` in `app/globals.css`, and add its background color to the bootstrap script in `app/layout.tsx`.

---

## State and Storage

| Key | Storage | Purpose |
| --- | --- | --- |
| `ascendara_session_id` | localStorage | Monitor session, sent as `X-Session-ID` |
| `ascendara_user_id` | localStorage | Account ID used by the legacy routes and decryption |
| `ascendara_session_timestamp` | sessionStorage | Expires the session after 24 hours within one tab |
| `ascendara-theme` | localStorage | Selected theme |
| `ascendara-artwork-cache` | localStorage | Artwork URL cache |
| `ascendara-install-prompt-dismissed` | localStorage | Hides the install prompt |
| `mock_mode` | localStorage | Development mock dashboard |
| `session_error` | sessionStorage | Makes `ConnectionGuard` show "Connection lost" |

---

## Building & Deployment

```bash
npm run build
npm start
```

Set `NEXT_PUBLIC_API_BASE_URL` **before** building, because Next.js inlines public variables into the browser bundle. Rebuild whenever the endpoint changes. Serve the app over HTTPS: an HTTPS frontend cannot call an HTTP monitor, and decryption needs the Web Crypto API, which only works in a secure context.

The monitor backend (`monitor-api.py`, Flask with Waitress, port 54303) is deployed separately. The `/companion/*` and `/library/*` routes only exist on a monitor version that includes them, so updating the frontend alone does not add them. See `readme/companion-deployment.md` in the webview repository for the server environment (`SECRET_KEY` or `ASCENDARA_SECRET_SEED`, the `ASCENDARA_BUILD_*` credentials, and `serviceAccountKey.json`).

---

## Troubleshooting

| Problem | What to check |
| --- | --- |
| `Failed to fetch` | Make sure the monitor is reachable, the build uses the intended API URL, and CORS allows the origin plus `Content-Type` and `X-Session-ID` |
| Downloads work but other pages return 404 | The monitor version doesn't have the `/companion/*` routes yet |
| Library shows "Desktop offline" | Ascendara must be open and signed in on the desktop; the library goes offline 45 seconds after the last sync |
| Play fails | Read the message returned from the desktop: the game may be running already, need a first launch in Ascendara, need Steam, be a VR game, or the desktop may be in developer mode |
| Cloud collection is empty | Sync the cloud library from the desktop app; the view reads `users/{uid}.cloudLibrary` |
| Artwork missing or slow | Check the monitor logs and the upstream signing/build credentials. Artwork is throttled on purpose, and the browser never needs a SteamGridDB key |
| Session expired or revoked | Reconnect with a new desktop code |
| Landing page status disagrees with a custom API | The status probe on `/` always checks `https://monitor.ascendara.app`, whatever `NEXT_PUBLIC_API_BASE_URL` is set to |
| Theme not saving | Check that `localStorage` is available (private browsing or blocked site data prevents saving) |

---

## Related Documentation

- [Ascendara Companion](/docs/features/companion)
- [Developer Overview](/docs/developer/overview)
- [Monitor API](/docs/api-reference/monitor-api)
- [API Reference](/docs/api-reference/overview)
- [System Design](/docs/developer/system-design)
- [Contributing Guide](/docs/getting-started/contributing)

---

## Resources

- **Repository**: [github.com/ascendara/webview](https://github.com/ascendara/webview)
- **Production**: [webview.ascendara.app](https://webview.ascendara.app/)
- **Discord**: [ascendara.app/discord](https://ascendara.app/discord)
