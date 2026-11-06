# Debrid Providers

How Ascendara's debrid integration is structured, and how to add a new provider. For the user guide, see [Debrid Services](/docs/downloads/debrid-services).

## Shared Service

Download and source-selection code goes through `src/services/downloads/debridService.js`. It:

- Selects the first eligible configured provider in the saved priority order.
- Applies host and torrent preferences.
- Dispatches downloads and records metadata.

`useDebridHosts` loads the enabled providers' cached domains. Callers don't compare provider IDs or read individual API-key settings.

## Provider Adapters

Each adapter in `src/services/downloads/debrid/` (TorBox, Real-Debrid, AllDebrid, Premiumize, Debrid-Link) supplies:

- Its identity (`id`, `name`), settings keys (`apiKeySetting`, `torrentSetting`, `prioritizeSetting`), `apiKeyUrl` for the **Get an API key** link, `downloadsPath`, and `hostDownloadMode` (`direct`, or `cloud` for TorBox, which processes hosted links in its cloud first).
- `getUserInfo(apiKey)`, used to check a key before saving it, and `describeAccount(user, t)`, returning `{ name, plan, expiresAt, expiryNote, stats }` for the account card. `stats` entries are `{ label, value, unit? }`, where `unit` is `bytes` or `days`.
- `seamlessHosts`: providers that keep their seamless download unless `prioritizeSetting` is on.
- Cached supported-host access and URL matching (`getCachedSupportedHosts`, `getSupportedHosts`, `isSupportedUrl`).
- `submitMagnet(link, apiKey, game)` returning `{ id }`, and `resolveHost(link, apiKey)` returning `{ id, status, url }`. A missing URL sends the user to the adapter's downloads page.
- A metadata storage key and serializer. The existing TorBox and Real-Debrid storage formats are preserved; the newer adapters only store torrents, since hosted links start right away.

Adapters may also expose:

- `signIn`: a code sign-in with `start()`, `poll(flow)`, `verificationUrl`, `clearedSettings` (applied when a different key is saved or the account is disconnected), `isSignedIn(settings)`, and an optional `signedInText`. `poll` resolves to `{ status: "pending" | "failed" }` or `{ status: "approved", settings }`. Real-Debrid uses its device flow for open source apps with a renewing refresh token; AllDebrid uses its PIN flow, which yields a plain API key.
- `getTorrentDownload(apiKey, id)`, returning `null` while processing, `"missing"` once the torrent is gone from the account, or a list of `{ url, filename, size }` records when ready. Throw an error with `terminal: true` when the service has given up on the torrent; it's reported once instead of retried.
- `getTorrentStatus(apiKey, id)`, returning `{ status, progress }` with `status` one of `queued`, `downloading`, `ready`, `failed`, or `missing`. It feeds the **Waiting at your debrid service** list and the 3-day stall check.
- `hasDownloadsPage: false` for providers without an account page. Their torrents are listed on the local Downloads page, and the settings card links there.
- `pollIntervalMs` to poll the handoff less often than the default 5-second pass (30 seconds for AllDebrid, Premiumize, and Debrid-Link).
- `removeTorrent(apiKey, id)` with `removeAfterDoneSetting`, for the opt-in cleanup of torrents whose game is on the PC. `removeTorrent` also backs the **Remove** button in the cloud list, and must treat an already-deleted torrent as success.
- `torrentFallbackSetting` for an explicitly enabled qBittorrent fallback after magnet submission fails (TorBox only).
- `resumePendingTorrents(apiKey)` for providers that need a separate startup step. It runs after submission and is polled by `DebridBackgroundTasks` independently of account pages. Real-Debrid uses it to select files once magnet metadata is ready, with persistent pending markers and concurrent-call deduplication.
- `getTorrentGame(entry)`, reading the adapter's stored game metadata.

## Settings and Routing

Debrid starts disabled (`debridEnabled: false`). The master switch reveals the account editor and enables routing. Turning it off keeps keys and order and pauses queued debrid downloads.

The settings live on their own **Settings → Debrid** page (`DebridSettings`). `debridProviderEnabled[id] === false` pauses a single service without clearing its key. A queued hosted link remembers its provider; if that provider is paused or disconnected, another configured provider that supports the link takes it.

Dragging priority rows or using their arrow buttons saves `debridProviderOrder`. TorBox is first when no order has been saved; an explicitly saved order always takes precedence. Hosted links choose the first compatible enabled account; magnets choose the first account with torrent support enabled. API errors don't trigger automatic submission to another account.

## Torrent Handoff

The shared handoff task checks only torrent IDs that Ascendara recorded. It waits for local downloads to finish, preserves the selected library, and persists `localDownloadStarted` after IPC accepts the transfer. A torrent whose progress doesn't move for 72 hours is marked `cloudTorrentStalled` and reported; one the service removed is marked `cloudTorrentMissing` and no longer polled.

The main process validates and caches a grouped file manifest and rejects duplicate or conflicting destinations. The [Downloader](/docs/binary-tool/downloader) handles the batch through its existing transfer, extraction, verification, and manual-install UI states.

## Adding a Provider

1. Implement the API service in `src/services/downloads/` and the adapter in `src/services/downloads/debrid/`, then register it in `debridProviders`.
2. Add its API proxy to `electron/modules/core/local-api-proxy.js` and `vite.config.js`, and its keychain-backed API key setting.
3. Add an icon to `DebridIcon`, and either an account downloads page or `hasDownloadsPage: false`.
4. Add a user guide in the docs and link it from the provider's `docsUrl`.

Source eligibility, preferred-provider selection, host caching, common download controls, and generic queue handling then use the adapter without new provider branches. The settings editor and reorderable priority list are generated from the registry.

## Testing

`createDebridService` accepts an adapter list for tests. The tests in `tests/` exercise expansion through the same public interface, each real adapter's normalized results, paused services, cloud torrent listing and cleanup, and the legacy TorBox and Real-Debrid metadata formats.

Run `npm test` for the mocked API contract tests and `npm run build` to verify the production renderer. Live account and download checks need a real account and suitable test links.
