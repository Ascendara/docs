# Monitor API

The monitor API at `monitor.ascendara.app` is a separate service from the [public API](/docs/api-reference/overview). It sits between the Ascendara desktop app and the [Companion webview](/docs/features/companion), and has no third-party use cases. Every endpoint needs either a signed-in desktop account or a paired Companion session, and it only accepts browser requests from the webview's own origin.

This page explains how it works, so you know what data the Companion moves around and where it goes. For the webview's code, see [Webview Development](/docs/developer/webview).

## How It Fits Together

```
Desktop app ──(Firebase ID token)──►  Monitor API  ◄──(X-Session-ID)── Companion webview
                                       │
                                       ├─ Firebase: accounts, paired devices, friends, cloud library
                                       ├─ api.ascendara.app: game index and artwork
                                       └─ data/ on the server: codes, sessions, downloads, command queues
```

The desktop and the webview never talk to each other directly:

- The **desktop** pushes its state (downloads, installed library, running games) and picks up queued commands.
- The **webview** reads that state and queues commands (pause a download, launch a game) for the desktop to pick up.

## Authentication

| Caller | How it authenticates |
| --- | --- |
| Desktop app | `Authorization: Bearer <Firebase ID token>` from the signed-in account. Each request may only touch that account's data. |
| Webview | `X-Session-ID: <session>`, the 32-character session from pairing. The monitor finds the account from the session, so the webview never sends a user ID it could change. |

Companion sessions are checked against the account's paired devices in Firebase on every request. A session stops working when it is disconnected (from the desktop or the webview) or after 7 days without activity.

## Pairing

1. In **Ascend → Companion**, the desktop calls `POST /generate-code`. The monitor returns a 6-digit code that is valid for 5 minutes. If the account already has an unused code that hasn't expired, the monitor returns that code with the time remaining.
2. The desktop polls `GET /connection-status/{code}` until the code is `connected` or `expired`.
3. The user enters the code in the webview (or scans the QR code), which calls `POST /verify-code`. Each code works once. The monitor creates a session, records the device in the account's `connected_devices` in Firebase with its platform, device type, and browser, and returns `sessionId`, `userId`, and `displayName`.
4. The desktop lists paired devices with `GET /connected-devices/{uid}`. Either side can unpair with `POST /disconnect-device`.

## Downloads

| Direction | Endpoint | What happens |
| --- | --- | --- |
| Desktop → monitor | `POST /downloads/sync` | Every 5 seconds while downloads are active, the desktop sends their progress, speed, ETA, and status |
| Desktop → monitor | `POST /downloads/notify-start` | A download has started (shown as "new" in the webview for 2 minutes) |
| Webview → monitor | `GET /downloads/{userId}` | Current downloads for the session's account |
| Webview → monitor | `POST /downloads/command` | Queues `pause`, `resume`, or `kill` for a download |
| Desktop → monitor | `GET /downloads/commands/{uid}` | The desktop picks up queued commands |
| Desktop → monitor | `POST /downloads/commands/acknowledge` | The desktop reports each command as `completed` or `failed` |

Download data that hasn't been updated for 2 hours is removed, so stale downloads don't linger after the desktop app is closed.

## Installed Library and Remote Launch

| Direction | Endpoint | What happens |
| --- | --- | --- |
| Desktop → monitor | `POST /library/sync` | Every 15 seconds, the desktop sends its installed games |
| Desktop → monitor | `POST /library/activity` | Every 3 seconds, the desktop sends which games are running |
| Webview → monitor | `GET /companion/library` | Installed games, plus whether the desktop is online |
| Webview → monitor | `POST /companion/library/launch` | Queues a launch for a game |
| Webview → monitor | `GET /companion/activity` | Games running on the desktop right now |
| Webview → monitor | `POST /companion/library/stop` | Queues a stop for a running game |
| Webview → monitor | `GET /companion/library/commands/{id}` | Status of a launch or stop request |
| Desktop → monitor | `GET /library/commands/{uid}` | The desktop picks up pending commands |
| Desktop → monitor | `POST /library/commands/claim` | The desktop claims a command, so only one desktop runs it |
| Desktop → monitor | `POST /library/commands/acknowledge` | The desktop reports the result as `completed` or `failed` |

What the desktop sends about each game:

- **Included:** an opaque game ID (a hash), the name, version, playtime, launch count, last played date, favorite status, description, size, categories, and online/DLC flags. Text fields are cut to a fixed length, and a library can hold at most 5,000 games.
- **Never included:** executable paths or folders.

Rules for remote commands:

- The desktop counts as **offline** when its last update is more than 45 seconds old. The monitor refuses launch and stop requests while it is offline.
- A launch is only accepted for a game ID in the desktop's latest snapshot.
- A stop is only accepted for a play session the desktop reported as running and stoppable.
- Commands go from `pending` to `processing` (claimed) to `completed` or `failed`. A command that isn't finished within 90 seconds reports `expired`.

## Account, Friends, and Catalog

These are read-only for the webview and always scoped to the session's account.

| Endpoint | Returns |
| --- | --- |
| `GET /companion/account` | Profile (name, avatar, bio, country, level) and the cloud library from `users/{uid}.cloudLibrary` |
| `POST /get-friends` | Friends and their presence |
| `GET /getusername` | The account's display name |
| `GET /companion/games` | Game index search with `q`, `category`, `sort` (`updated`, `popular`, `name`), and `page` |
| `GET /companion/games/{id}` | One game's details |
| `GET /companion/artwork?q=...` | Cover art URLs for up to 40 game names |

The catalog comes from Ascendara's game index and is cached for 5 minutes. If the index can't be reached, the last copy is served for up to an hour and marked `stale`. Catalog responses contain metadata and system requirements, never download links.

Artwork is resolved through the public API's SteamGridDB proxy with a signed server-to-server request, so the browser never needs a SteamGridDB key. Results are cached on the monitor.

## Data and Retention

The monitor keeps its working state in files under `data/` on the server, encrypted at rest: connection codes, sessions, downloads, command queues, and the installed-library snapshots. Paired devices are stored in Firebase under the account. A cleanup job runs every 30 minutes and removes:

| Data | Removed after |
| --- | --- |
| Unused connection codes | 5 minutes |
| Sessions with no activity | 7 days |
| Download data that stopped updating | 2 hours |
| Download-started notifications | 24 hours |
| Finished download commands | 1 hour |

Library commands older than 5 minutes are cleared whenever a new launch is queued.

## Rate Limits

Limits are per IP address. They are set for normal use of the desktop app and webview, for example:

| Endpoint | Limit |
| --- | --- |
| `POST /generate-code` | 10 per minute |
| `POST /verify-code` | 20 per minute |
| `POST /companion/library/launch`, `/stop` | 10 per minute |
| `GET /companion/artwork`, `/companion/account` | 30 per minute |
| `GET /companion/games` | 60 per minute |
| `POST /library/sync` | 12 per minute |
