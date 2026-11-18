# Plugin API Reference

Your plugin's `start` function receives an `ascendara` object. This page lists everything on it.

```javascript
module.exports = {
  start(ascendara) { /* ... */ },
  stop() { /* optional */ },
};
```

Anything you add through `ascendara` (styles, listeners, game page buttons) is removed automatically when the plugin is turned off, updated, or uninstalled. Methods that reach outside the page return Promises; use `await`.

Errors from these calls are normal `Error` objects with a readable message, for example `Add "processes" to the plugin's permissions to use run`.

## Plugin information

| Property | Description |
| --- | --- |
| `id` | The plugin's id (its folder name) |
| `version` | The plugin's version from `manifest.json` |
| `appVersion` | The Ascendara version, for example `"12.4.0"` |
| `platform` | `"win32"`, `"darwin"` (macOS), or `"linux"` |
| `permissions` | The permissions from `manifest.json` |
| `dataPath` | Absolute path to the plugin's data folder |

## Interface

### `addStyle(css)`

Adds CSS to the page. Returns a function that removes it.

```javascript
ascendara.addStyle(`[data-nav-path="/workshopdownloader"] { display: none !important; }`);
```

Navigation buttons have a `data-nav-path` attribute with their route.

### `onNavigate(callback)`

Calls `callback(route)` immediately with the current route, then again every time it changes. Routes look like `/library`, `/gamescreen`, or `/settings`. Returns a function that stops listening.

```javascript
ascendara.onNavigate(route => {
  document.body.classList.toggle("my-plugin-on-library", route === "/library");
});
```

### `navigate(route)`

Goes to a route, for example `ascendara.navigate("/downloads")`.

### `toast(message, type)`

Shows a notification. `type` is `"info"` (default), `"success"`, `"warning"`, or `"error"`.

### `log(...args)`

Writes to the developer console with your plugin's id in front.

### `onCleanup(fn)`

Runs `fn` when the plugin is turned off. Use it for things you set up yourself:

```javascript
const timer = setInterval(checkSomething, 60000);
ascendara.onCleanup(() => clearInterval(timer));
```

### `gameScreen.addAction({ label, onClick, when })`

Adds an item to the **Manage** menu on a game's page. Returns a function that removes it.

| Option | Description |
| --- | --- |
| `label` | The menu item's text |
| `onClick(game)` | Called with the game the page is showing. Can be `async`. If it throws, Ascendara shows the error as a toast. |
| `when(game)` | Optional. Return `false` to hide the item for this game. |

The `game` object is the same one Ascendara uses for the page. Useful fields: `game` (or `name` for some imported games), `isCustom`, `executable`, `version`, and `playTime`.

## Games

### `games.directory(game)`

Returns the absolute path of a game's install folder, or `null` if it can't be found. Pass the `game` object from `addAction`.

### `games.onLaunch(callback)` / `games.onExit(callback)`

Calls `callback(gameName)` when a game started from Ascendara launches or closes. Returns a function that stops listening.

```javascript
ascendara.games.onLaunch(name => ascendara.log(`${name} started`));
```

## Saving settings

### `storage.get(key, fallback)` / `storage.set(key, value)` / `storage.remove(key)`

Small values saved for your plugin, such as user preferences. Values must be JSON-compatible. These are synchronous.

```javascript
const count = ascendara.storage.get("launches", 0);
ascendara.storage.set("launches", count + 1);
```

## Files

Every plugin has its own data folder (`dataPath`). These methods take paths relative to it, and refuse paths that point outside it. No permission is needed.

| Method | Description |
| --- | --- |
| `files.read(path)` | Reads a text file |
| `files.write(path, text)` | Writes a text file, creating folders as needed |
| `files.exists(path)` | `true` if the file or folder exists |
| `files.list(folder)` | Lists a folder as `[{ name, directory }]`. Defaults to the data folder itself. |
| `files.remove(path)` | Deletes a file or folder |

The data folder is deleted when the plugin is uninstalled.

## Downloads

Requires the `downloads` permission.

### `download(url, path, { onProgress })`

Downloads `url` to `path` in the data folder and returns the file's absolute path. The URL must use `https://`. Redirects are followed, so GitHub release links work. Downloads are limited to 4 GB.

```javascript
await ascendara.download(url, "downloads/tool.zip", {
  onProgress: ({ received, total }) => ascendara.log(`${received} / ${total} bytes`),
});
```

`total` is `0` when the server doesn't report a size.

### `extract(archive, folder)`

Extracts an archive in the data folder into another folder in the data folder, and returns the folder's absolute path.

- **`.zip`** is extracted by Ascendara itself and works everywhere.
- **Other formats** (`.tar`, `.tar.gz`, `.tgz`, `.tar.xz`, `.7z`) use the `tar` tool built into Windows, macOS, and Linux. Which formats it reads depends on the system. macOS reads all of them. Linux usually can't read `.7z`, and some Windows versions can't either.
- **`.7z`**: if `tar` can't read it, Ascendara tries 7-Zip (`7z`, `7zz`, or `7za` on the `PATH`, or `C:\Program Files\7-Zip\7z.exe`).

Prefer `.zip` downloads when a project offers them. If you need `.7z`, catch the error and tell the user to install 7-Zip.

Archives with entries that point outside the destination folder are refused.

## Programs

Requires the `processes` permission. Program paths can be absolute (for example, a game's `.exe`) or relative to the data folder (for example, an emulator you downloaded).

### `run(program, args, { cwd, onExit })`

Starts a program and returns `{ pid, kill() }` without waiting for it to close. `cwd` defaults to the program's folder. `onExit(code)` is called when it closes.

```javascript
const emulator = await ascendara.run("emulators/dolphin/Dolphin.exe", ["--batch", "--exec", romPath], {
  onExit: code => ascendara.log(`Dolphin closed with code ${code}`),
});
```

Programs keep running if the plugin is turned off. They're closed if the plugin is uninstalled.

### `exec(program, args, { cwd, timeout })`

Runs a program, waits for it to finish, and returns `{ code, stdout, stderr }`. Use it for command-line tools. `timeout` is in milliseconds: 60 seconds by default, 10 minutes at most. Output is limited to 10 MB.

```javascript
const { code, stdout } = await ascendara.exec("tools/checker.exe", ["--version"]);
```

### `openPath(path)`

Opens a file or folder with its default app, the same as double-clicking it.

## Going further

`window.electron` is the same API Ascendara's own pages use, and plugins can call it too. It isn't a stable plugin API: methods can change between Ascendara versions. See the [IPC Reference](/docs/developer/ipc-reference), and set `minAppVersion` if you rely on it.

For example, a plugin can set up a [Retro](/docs/features/retro) console with an emulator it downloaded, using `window.electron.retro.saveProfile`. See [Examples](/docs/plugins/examples#downloading-an-emulator-for-retro).
