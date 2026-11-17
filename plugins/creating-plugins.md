# Creating Your First Plugin

This guide walks you through writing a plugin from an empty folder to something you can share. You need a text editor and a copy of Ascendara. No build tools are required.

## How plugins work

A plugin is a folder with two files:

```
my-plugin/
├── manifest.json   # Name, version, author, and what the plugin needs
└── index.js        # The code
```

When the plugin is turned on, Ascendara runs `index.js` inside its own window. Your code gets an `ascendara` object with helpers for common jobs, and it can also change anything on the page directly, the same way a browser extension can change a website. Ascendara is [open source](https://github.com/Ascendara/ascendara), so you can read how any page is built and change it.

There are no fixed "hook points" you're limited to. The `ascendara` helpers make common changes easy and undo them for you when the plugin is turned off, but you're free to go further.

## Step 1: Create the folder

Open **Settings** → **Plugins** → **Open plugins folder**, and create a folder there named after your plugin.

The folder name is your plugin's **id**. Use lowercase letters, numbers, and dashes, for example `compact-library` or `steam-grid-covers`. It must be unique in the official list.

Working directly in the plugins folder means you can edit the files and reload without copying anything.

## Step 2: Write the manifest

Create `manifest.json`:

```json
{
  "name": "Hello Ascendara",
  "version": "1.0.0",
  "author": "your-name",
  "description": "Says hello when you open a game.",
  "apiVersion": 1
}
```

`name`, `version`, and `author` are required. `version` must be three numbers, like `1.0.0`. See [Manifest and Versioning](/docs/plugins/manifest) for every field.

## Step 3: Write the code

Create `index.js`:

```javascript
module.exports = {
  start(ascendara) {
    ascendara.toast("Hello from my plugin!");

    ascendara.onNavigate(route => {
      if (route.startsWith("/gamescreen")) {
        ascendara.log("Opened a game page");
      }
    });
  },

  stop() {
    // Optional. Anything added with the ascendara helpers is cleaned up for you.
  },
};
```

- `start(ascendara)` runs when the plugin is turned on, and each time Ascendara starts with the plugin on. It can be `async`.
- `stop()` runs when the plugin is turned off, updated, or uninstalled. You only need it for things you set up yourself, such as a `setInterval`.

## Step 4: Turn it on

1. Go to **Settings** → **Plugins** → **Installed** and choose **Refresh**.
2. Your plugin appears with a **Sideloaded** badge. Turn on its switch and confirm.

You should see the toast right away.

## Step 5: Edit and reload

After changing your files, turn the plugin off and on again. Ascendara reads the files from disk each time, so there's no need to restart the app.

If something goes wrong:

- **The plugin's card shows a problem.** Errors in `manifest.json`, and errors thrown while `start` runs, show up there.
- **Logs.** `ascendara.log(...)` and `console.log(...)` write to the developer console. DevTools are available when you [run Ascendara from source](/docs/developer/build-from-source) and on the Experimental and Public Testing branches (**Settings** → **Advanced** → **App branch**). Open them from the [System Center](/docs/features/system-center) with **Developer diagnostics**. Your plugin's code shows up in the **Sources** tab as `ascendara-plugin://<id>.js`.
- **Ascendara won't load.** Start it in [safe mode](/docs/plugins/overview#safe-mode) and turn the plugin off.

## A real example: hiding a navigation button

Each button in the navigation bar has a `data-nav-path` attribute you can target with CSS. This is the complete code of the official **Hide Ascend** plugin:

```javascript
module.exports = {
  start(ascendara) {
    ascendara.addStyle('[data-nav-path="/ascend"] { display: none !important; }');
  },
};
```

`addStyle` adds the CSS to the page and removes it automatically when the plugin is turned off.

For CSS-only plugins, you can skip the JavaScript and point the manifest at a stylesheet instead. No `index.js` is needed:

```json
{
  "name": "Compact Library",
  "version": "1.0.0",
  "author": "your-name",
  "style": "style.css"
}
```

## Adding a button to the game page

`ascendara.gameScreen.addAction` adds an item to the **Manage** menu on every game's page:

```javascript
module.exports = {
  start(ascendara) {
    ascendara.gameScreen.addAction({
      label: "Copy game folder path",
      async onClick(game) {
        const folder = await ascendara.games.directory(game);
        await navigator.clipboard.writeText(folder);
        ascendara.toast("Copied!", "success");
      },
    });
  },
};
```

Pass `when: game => ...` to show the item only for some games.

## Finding things to change

- Read the page you want to change in the [Ascendara source](https://github.com/Ascendara/ascendara/tree/main/src/pages). Routes match the page names, for example `/library`, `/downloads`, and `/gamescreen`.
- In DevTools, inspect an element to find a selector for it.
- Prefer stable selectors such as `data-nav-path`, `data-tour`, and `id` attributes over Tailwind class names. Class names change often between Ascendara versions.

Plugins that reach into the page directly can break when Ascendara updates. Test your plugin on new versions, and use `minAppVersion` in your manifest when you rely on something new.

## Next steps

- [Plugin API Reference](/docs/plugins/api-reference): everything on the `ascendara` object
- [Examples](/docs/plugins/examples): downloading and launching an emulator, running tools for a game, and more
- [Manifest and Versioning](/docs/plugins/manifest): fields, permissions, and how updates work
- [Publishing Plugins](/docs/plugins/publishing): get your plugin into the official list
