# Manifest and Versioning

Every plugin has a `manifest.json` in its folder. Ascendara reads it to show the plugin in **Settings** → **Plugins**, decide whether it can run, and know what it's allowed to do. Before a plugin is installed, updated or turned on, Ascendara shows a dialog with everything in the manifest: the description and details, what the plugin changes, what it can access, its files and sizes, and its license and website.

## Example

```json
{
  "name": "Emulator Helper",
  "version": "1.2.0",
  "author": "your-name",
  "description": "Downloads and sets up emulators for Retro.",
  "details": "Picks the right emulator for each console you add in Retro, downloads it, and points Retro at it.",
  "affects": [
    "Adds a Set up button to each console in Retro",
    "Downloads emulators into the plugin's folder"
  ],
  "changelog": "Adds PPSSPP for PSP games.",
  "license": "MIT",
  "homepage": "https://github.com/your-name/emulator-helper",
  "apiVersion": 1,
  "minAppVersion": "12.4.0",
  "permissions": ["downloads", "processes"],
  "main": "index.js",
  "style": "style.css"
}
```

## Fields

| Field | Required | Description |
| --- | --- | --- |
| `name` | Yes | Name shown in Ascendara. Up to 100 characters. |
| `version` | Yes | The plugin's version, as three numbers: `major.minor.patch`, for example `1.2.0`. See [Versioning](#versioning). |
| `author` | Yes | Your name or handle. Up to 100 characters. |
| `description` | No | One or two sentences about what the plugin does. Up to 500 characters. |
| `details` | No | A longer explanation shown in the install dialog: who it's for, how to use it, anything to know first. Line breaks are kept. Up to 4000 characters. |
| `affects` | No | A list of what the plugin changes in Ascendara, one short line each, such as `"Hides the Ascend button in the navigation bar"`. Shown under **What it changes**. Up to 20 lines of 200 characters. |
| `changelog` | No | What changed in this version. Shown when users update. Up to 2000 characters. |
| `license` | No | The plugin's license, for example `MIT`. Up to 50 characters. |
| `homepage` | No | An `https://` link to the plugin's source or page. |
| `apiVersion` | No | The version of the plugin API the plugin was written for. Currently `1`, which is also the default. |
| `minAppVersion` | No | The oldest Ascendara version the plugin works with, for example `12.4.0`. |
| `permissions` | No | Things the plugin needs to do outside the app. See [Permissions](#permissions). |
| `main` | No | The script to run. Defaults to `index.js`. Not needed for plugins that only have a `style`. |
| `style` | No | A CSS file added to the page while the plugin is on. |

The plugin's **id** isn't in the manifest. It's the name of the plugin's folder: lowercase letters, numbers, and dashes, up to 64 characters, starting with a letter or number. The id must never change once the plugin is published, because Ascendara uses it to match updates to the installed plugin.

`main` and `style` must point to files inside the plugin's folder. Each file can be up to 2 MB, and a plugin can be up to 50 MB in total.

## Permissions

Any plugin can change Ascendara's interface, save settings, and read and write files in its own data folder. A plugin that needs to do more must list it in `permissions`:

| Permission | Allows |
| --- | --- |
| `downloads` | `ascendara.download()` and `ascendara.extract()` |
| `processes` | `ascendara.run()`, `ascendara.exec()`, `ascendara.openPath()`, and stopping programs the plugin started |

Ascendara shows these in plain language before the plugin is installed or turned on, and refuses those calls from a plugin that didn't list the permission. When an update asks for a permission the installed version didn't have, the update dialog marks it as new.

Ask only for what you use. Reviewers reject plugins that ask for permissions they don't need, or whose `affects` leaves out something the plugin changes.

## Versioning

Plugin versions use three numbers, `major.minor.patch`:

- **Patch** (`1.2.0` → `1.2.1`): bug fixes.
- **Minor** (`1.2.1` → `1.3.0`): new features that don't change how existing ones behave.
- **Major** (`1.3.0` → `2.0.0`): changes users need to know about, such as removed features or settings that reset.

Versions are compared number by number, so `1.10.0` is newer than `1.9.0`.

Ascendara offers an update when the official list has a higher `version` than the one installed. **Every change to a published plugin needs a new version**, even a one-line fix. The official list pins each version's files, so changing files without changing the version doesn't reach users.

## Compatibility

Two fields keep a plugin from running on an Ascendara version it doesn't support:

- **`minAppVersion`**: set this when your plugin relies on something added in a specific Ascendara version, such as a new helper or a page that changed. Older versions of Ascendara show "Needs Ascendara x.y.z or newer" and don't run the plugin. The official list shows the same message instead of an **Install** button.
- **`apiVersion`**: the plugin API's version. It only increases when a change would break existing plugins, such as a helper being removed or behaving differently. New helpers are added without changing it. A plugin with a higher `apiVersion` than the installed Ascendara supports shows "Needs a newer version of Ascendara".

Plugins that change the page directly, instead of through `ascendara` helpers, can break when Ascendara's interface changes, whatever these fields say. Test your plugin when a new Ascendara version comes out.
