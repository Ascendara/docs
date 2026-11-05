# Debrid Providers

How Ascendara's debrid integration is structured, and how to add a new provider. For the user guide, see [Debrid Services](/docs/features/debrid-services).

## Shared Service

Download and source-selection code goes through `src/services/downloads/debridService.js`. It:

- Selects the first eligible configured provider in the saved priority order.
- Applies host and torrent preferences.
- Dispatches downloads and records metadata.

`useDebridHosts` loads the enabled providers' cached domains. Callers don't compare provider IDs or read individual API-key settings.

## Provider Adapters

Each adapter in `src/services/downloads/debrid/` supplies:

- Its identity, existing settings keys, downloads-page route, and whether hosted links download directly or process in the cloud.
- Cached supported-host access and URL matching.
- `submitMagnet(link, apiKey, game)` returning `{ id }`, and `resolveHost(link, apiKey)` returning `{ id, status, url }`. A missing URL sends the user to the adapter's downloads page.
- A metadata storage key and serializer for its downloads page. The existing TorBox and Real-Debrid storage formats are preserved.
- An optional setting for an explicitly enabled qBittorrent fallback after magnet submission fails.
- An optional `resumePendingTorrents(apiKey)` for providers that need a separate startup step. It runs after submission and is polled by `DebridBackgroundTasks` independently of account pages. Real-Debrid uses it to select files once magnet metadata is ready, with persistent pending markers and concurrent-call deduplication.

Adapters may also expose:

- `getTorrentDownload(apiKey, id)`, returning `null` while processing, or a list of `{ url, filename, size }` records when ready.
- `getTorrentGame(entry)`, reading the adapter's stored game metadata.

## Settings and Routing

Debrid starts disabled (`debridEnabled: false`). The master switch reveals the account editor and enables routing. Turning it off keeps keys and order and pauses queued debrid downloads.

Dragging priority rows or using their arrow buttons saves `debridProviderOrder`. TorBox is first when no order has been saved; an explicitly saved order always takes precedence. Hosted links choose the first compatible enabled account; magnets choose the first account with torrent support enabled. API errors don't trigger automatic submission to another account.

## Torrent Handoff

The shared handoff task checks only torrent IDs that Ascendara recorded. It waits for local downloads to finish, preserves the selected library, and persists `localDownloadStarted` after IPC accepts the transfer.

The main process validates and caches a grouped file manifest and rejects duplicate or conflicting destinations. The [Downloader](/docs/binary-tool/downloader) handles the batch through its existing transfer, extraction, verification, and manual-install UI states.

## Adding a Provider

1. Implement the adapter and register it in `debridProviders`.
2. Add its credentials/settings UI, encrypted setting, API proxy, and account downloads page as needed.

Source eligibility, preferred-provider selection, host caching, common download controls, and generic queue handling then use the adapter without new provider branches. The settings editor and reorderable priority list are generated from the registry.

## Testing

`createDebridService` accepts an adapter list for tests. The tests include a third adapter to exercise expansion through the same public interface, plus both real adapters' normalized results and legacy metadata formats.

Run `npm test` for the mocked API contract tests and `npm run build` to verify the production renderer. Live account and download checks need a real account and suitable test links.
