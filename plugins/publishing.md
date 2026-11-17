# Publishing Plugins

Official plugins appear for everyone under **Settings** → **Plugins** → **Browse**. They live in the [`plugins` folder of the Ascendara repository](https://github.com/Ascendara/ascendara/tree/main/plugins), and every version is reviewed by the Ascendara team before it's listed.

## Before you submit

Make sure your plugin:

- **Works on a current version of Ascendara**, installed with **Install from folder** and turned off and on again without leaving anything behind.
- **Has a complete `manifest.json`**: a clear `name` and `description`, your `author` name, a `version`, and `minAppVersion` if it relies on anything recent. See [Manifest and Versioning](/docs/plugins/manifest).
- **Asks only for the permissions it uses.**
- **Is readable.** Submit the source as you wrote it. Minified or obfuscated code is rejected, since reviewers have to be able to read every line.
- **Has no bundled programs.** If your plugin needs a program, download it from the program's official source with `ascendara.download()` at runtime.
- **Doesn't collect data.** Plugins mustn't send information about the user, their library, or their accounts anywhere, unless that's the plugin's clear, stated purpose and the user turns it on.

## Submitting

1. Put your plugin's source somewhere public, such as a GitHub repository.
2. Join the [Ascendara Discord](https://ascendara.app/discord) and post in the plugin submissions channel with:
   - the plugin's name and what it does
   - a link to the source
   - the version you're submitting
   - which permissions it uses, and why
3. A maintainer reviews it and may ask for changes.
4. Once it's approved, the maintainer adds it to the `plugins` folder. It appears in **Browse** for everyone once that change reaches the `main` branch.

## Updating your plugin

1. Make your changes and **increase `version`** in `manifest.json`. See [Versioning](/docs/plugins/manifest#versioning).
2. Post the update in the same Discord channel with a short list of what changed.
3. After review, the new version replaces the old one in the official list, and users see an **Update** button.

Users choose when to update, so make sure a new version works with whatever the previous one saved, such as `storage` values and files in the data folder.

## How the official list works

This section is for maintainers, and for anyone curious about how Ascendara keeps installs safe.

Each plugin is a folder in `plugins/`, named after its id. `plugins/index.json` lists every plugin's manifest, along with each of its files and that file's SHA-256 hash. Ascendara downloads `index.json` from GitHub, and when you install or update a plugin, it downloads each file and checks its hash. If any file doesn't match, the install stops and nothing changes, so users always get exactly the version that was reviewed.

After adding or updating a plugin folder, regenerate the index from the repository root:

```bash
node scripts/build-plugin-index.cjs
```

The script checks every manifest with the same rules Ascendara uses, and fails if a plugin is invalid, references a missing file, or is larger than 50 MB. `npm test` fails when `index.json` is out of date, so a stale index can't be merged by accident.

Ascendara reads the list from the `main` branch, so a plugin added on another branch isn't visible until it's merged. GitHub and Ascendara each cache the list for about five minutes.

To try the list before merging, [run Ascendara from source](/docs/developer/build-from-source). The **Browse** tab then has a **This checkout** / **GitHub main** switch, and **This checkout** reads `plugins/` from your local copy of the repository, hash checks included.

Files in `plugins/` are stored with their exact bytes (see `.gitattributes`), so line-ending conversion on Windows doesn't change their hashes.
