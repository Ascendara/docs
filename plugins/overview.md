# Plugins

Plugins are community add-ons that change how Ascendara looks and works. A plugin can restyle the app, hide or add parts of the interface, add buttons to a game's page, react when games start and stop, and, with your permission, download tools such as emulators and run programs.

Plugins are made by the community. Official plugins are reviewed before they're published, but every plugin runs with full access to Ascendara, so only install plugins you trust.

## Installing a plugin

### From the official list

1. Open **Settings** → **Plugins**.
2. Open the **Browse** tab.
3. Choose **Install** on the plugin you want.
4. Read the plugin's details: what it does, what it changes in Ascendara, what it can access (such as running programs), and its files. Choose **Install** to confirm.

The plugin is turned on as soon as it's installed. Official plugins show an **Official** badge.

### From a folder

You can also install a plugin someone sent you, or one you're writing yourself:

1. Open **Settings** → **Plugins** → **Installed**.
2. Choose **Install from folder** and select the plugin's folder (the one that contains `manifest.json`).
3. Ascendara shows the plugin's details. Nothing is copied until you choose **Install**, and the plugin is turned on once it's installed. Nobody has reviewed a plugin installed this way, so it shows a **Sideloaded** badge.

## Turning plugins on and off

Use the switch next to a plugin under **Settings** → **Plugins** → **Installed**. Changes take effect immediately, without restarting Ascendara.

If a plugin can't run, its card shows the problem, for example a broken `manifest.json` or a plugin that needs a newer version of Ascendara.

## Updating plugins

Ascendara checks the official list when you open **Settings** → **Plugins**. When a newer version of an installed plugin is available, the **Installed** tab shows a count, and the plugin shows an **Update to x.y.z** button. It opens the update's details, including what's new and any permissions the new version adds. Updates are never installed without you choosing to.

Sideloaded plugins don't update automatically. Install the new version from its folder after uninstalling the old one.

## Permissions

Every plugin can change Ascendara's interface and save its own settings. Some plugins also need to do things outside the app. They have to list these in their manifest, and Ascendara shows them before you install or turn on the plugin:

| Permission | What it allows |
| --- | --- |
| **Download files from the internet** | Download files, such as an emulator, into the plugin's own folder and extract archives there |
| **Run programs on your computer** | Start programs, including ones the plugin downloaded, and open files |

Permissions tell you what a plugin intends to do. They aren't a sandbox: a plugin can reach anything the Ascendara window can, including your accounts and downloads. That's why the official list is reviewed, and why sideloaded plugins show a stronger warning.

## Safe mode

If a plugin breaks Ascendara, for example by leaving you with a blank window, start Ascendara in safe mode. Safe mode turns off every plugin for that session, without uninstalling anything:

- **Windows**: Right-click your Ascendara shortcut → **Properties**, add ` --safe-mode` to the end of **Target**, and open Ascendara with that shortcut.
- **macOS**: In Terminal, run `open -a Ascendara --args --safe-mode`.
- **Linux**: Run `~/.local/bin/Ascendara.AppImage --safe-mode` (or the path to your AppImage).

**Settings** → **Plugins** shows a notice while safe mode is on. Turn off or uninstall the plugin that caused the problem, then start Ascendara normally.

## Uninstalling

Choose the trash icon on the plugin's card. This removes the plugin and everything it saved in its folder, such as emulators it downloaded. Programs the plugin started are closed.

## Where plugins are stored

| | Windows | macOS | Linux |
| --- | --- | --- | --- |
| Plugins | `%APPDATA%\ascendara\plugins\` | `~/Library/Application Support/ascendara/plugins/` | `~/.config/ascendara/plugins/` |
| Plugin data (downloads, files) | `%APPDATA%\ascendara\plugin-data\` | `~/Library/Application Support/ascendara/plugin-data/` | `~/.config/ascendara/plugin-data/` |

**Open plugins folder** in **Settings** → **Plugins** opens the first one.

## Making your own

Plugins are plain JavaScript. If you've written a little JavaScript before, you can write a plugin. Start with [Creating Your First Plugin](/docs/plugins/creating-plugins).
