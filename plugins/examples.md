# Plugin Examples

Complete plugins you can copy and adapt. Each one is a `manifest.json` and an `index.js` in a folder; see [Creating Your First Plugin](/docs/plugins/creating-plugins) for how to install and reload them.

## Downloading an emulator for Retro

This plugin adds a one-click emulator setup for [Retro](/docs/features/retro): it downloads DuckStation, extracts it into the plugin's folder, and sets it as the PlayStation 1 emulator. Retro then launches games with it as usual.

`manifest.json`:

```json
{
  "name": "DuckStation Installer",
  "version": "1.0.0",
  "author": "your-name",
  "description": "Downloads DuckStation and sets it up for PlayStation 1 in Retro.",
  "permissions": ["downloads"]
}
```

`index.js`:

```javascript
// Release file names change over time. Check the project's releases page.
const DOWNLOAD =
  "https://github.com/stenzek/duckstation/releases/download/latest/duckstation-windows-x64-release.zip";
const EXECUTABLE = "emulators/duckstation/duckstation-qt-x64-ReleaseLTCG.exe";

async function install(ascendara) {
  ascendara.toast("Downloading DuckStation…");
  await ascendara.download(DOWNLOAD, "downloads/duckstation.zip", {
    onProgress: ({ received, total }) =>
      total && ascendara.log(`${Math.round((received / total) * 100)}%`),
  });
  await ascendara.extract("downloads/duckstation.zip", "emulators/duckstation");
  await ascendara.files.remove("downloads");

  // Keep the user's ROM folders and save folder; only set the emulator.
  const state = await window.electron.retro.getState();
  if (!state.success) throw new Error(state.error);
  const current = state.data.profiles.ps1 || {};
  const saved = await window.electron.retro.saveProfile("ps1", {
    romFolders: [],
    saveFolder: "",
    fullscreen: true,
    ...current,
    adapter: "duckstation",
    executable: `${ascendara.dataPath}/${EXECUTABLE}`,
  });
  if (!saved.success) throw new Error(saved.error);
  ascendara.toast("DuckStation is ready for PlayStation 1 in Retro", "success");
}

module.exports = {
  async start(ascendara) {
    if (ascendara.platform !== "win32") return;
    if (await ascendara.files.exists(EXECUTABLE)) return;
    try {
      await install(ascendara);
    } catch (error) {
      ascendara.toast(`DuckStation setup failed: ${error.message}`, "error");
    }
  },
};
```

Things to notice:

- `download` and `extract` take paths inside the plugin's data folder. `dataPath` turns them into the absolute path Retro needs.
- The plugin checks whether the emulator is already there, so it only downloads once.
- Retro's own launch presets (`duckstation`, `pcsx2`, `dolphin`, and so on) handle the launch arguments. Use `adapter: "custom"` with `arguments` for other emulators. See [Custom emulator arguments](/docs/features/retro#custom-emulator-arguments).
- `window.electron.retro` isn't part of the stable plugin API. Set `minAppVersion` and test on new Ascendara versions.

## Running a tool alongside a game

Some games work better with a helper program running, such as an overlay, a controller mapper, or a performance tool. This plugin starts one when any game launches and closes it when the game exits.

`manifest.json`:

```json
{
  "name": "Overlay Companion",
  "version": "1.0.0",
  "author": "your-name",
  "description": "Starts your overlay with every game and closes it afterwards.",
  "permissions": ["processes"]
}
```

`index.js`:

```javascript
module.exports = {
  start(ascendara) {
    const running = new Map();

    ascendara.games.onLaunch(async gameName => {
      const program = ascendara.storage.get("program");
      if (!program || running.has(gameName)) return;
      try {
        running.set(gameName, await ascendara.run(program, []));
      } catch (error) {
        ascendara.toast(`Couldn't start the overlay: ${error.message}`, "error");
      }
    });

    ascendara.games.onExit(gameName => {
      running.get(gameName)?.kill();
      running.delete(gameName);
    });

    // Let the user choose the program from any game's Manage menu.
    ascendara.gameScreen.addAction({
      label: "Choose overlay program",
      async onClick() {
        const program = await window.electron.openFileDialog();
        if (!program) return;
        ascendara.storage.set("program", program);
        ascendara.toast("Overlay program saved", "success");
      },
    });
  },
};
```

## Adding a game tool to the Manage menu

Run a program on the selected game, for example a mod manager that takes the game's folder:

```javascript
module.exports = {
  start(ascendara) {
    ascendara.gameScreen.addAction({
      label: "Open in Mod Manager",
      when: game => !game.isCustom,
      async onClick(game) {
        const folder = await ascendara.games.directory(game);
        if (!folder) throw new Error("Couldn't find this game's folder");
        await ascendara.run("tools/modmanager/ModManager.exe", ["--game", folder]);
      },
    });
  },
};
```

This needs the `processes` permission. If the action throws, Ascendara shows the error message as a toast.

## Changing a page directly

When there's no helper for what you want, change the page yourself and clean up in `onCleanup`. This adds a line of text to the top of the Library page:

```javascript
module.exports = {
  start(ascendara) {
    let note;

    ascendara.onNavigate(route => {
      note?.remove();
      note = null;
      if (route !== "/library") return;
      // Wait a moment for the page to render.
      setTimeout(() => {
        const main = document.querySelector("main") || document.body;
        note = document.createElement("p");
        note.textContent = "Hello from my plugin!";
        note.style.cssText = "padding: 8px 16px; opacity: 0.7;";
        main.prepend(note);
      }, 300);
    });

    ascendara.onCleanup(() => note?.remove());
  },
};
```

Ascendara's pages are React components and can re-render. For anything more than a small addition, prefer CSS with `addStyle`, which keeps working when a page re-renders.

## Restyling Ascendara

A CSS-only plugin needs no script:

`manifest.json`:

```json
{
  "name": "Rounded Covers",
  "version": "1.0.0",
  "author": "your-name",
  "style": "style.css"
}
```

`style.css`:

```css
/* Hide the Workshop button and round off images. */
[data-nav-path="/workshopdownloader"] {
  display: none !important;
}

img {
  border-radius: 12px;
}
```

For colors, use a theme instead (**Settings** → **General** → **Appearance**). Themes are easier to share and switch between than CSS.
