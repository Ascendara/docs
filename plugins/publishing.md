# Publishing Plugins

Official plugins appear for everyone under **Settings** → **Plugins** → **Browse**. They live in the [`plugins` folder of the Ascendara repository](https://github.com/Ascendara/ascendara/tree/development/plugins), and every version is reviewed by the Ascendara team before it's listed.

## Before you submit

Make sure your plugin:

- **Works on a current version of Ascendara**, installed with **Install from folder** and turned off and on again without leaving anything behind.
- **Has a complete `manifest.json`**: a clear `name` and `description`, your `author` name, a `version`, and `minAppVersion` if it relies on anything recent. See [Manifest and Versioning](/docs/plugins/manifest).
- **Asks only for the permissions it uses.**
- **Is readable.** Submit the source as you wrote it. Minified or obfuscated code is rejected, since reviewers have to be able to read every line.
- **Has no bundled programs.** If your plugin needs a program, download it from the program's official source with `ascendara.download()` at runtime.
- **Doesn't collect data.** Plugins mustn't send information about the user, their library, or their accounts anywhere, unless that's the plugin's clear, stated purpose and the user turns it on.

## Submitting

Plugins are submitted as pull requests to the `development` branch of the [Ascendara repository](https://github.com/Ascendara/ascendara). You need a GitHub account and Node.js 22.

1. [Fork the repository](https://github.com/Ascendara/ascendara/fork) and create a branch from `development`.
2. Copy your plugin's folder into `plugins/`. The folder name is your plugin's id, for example `plugins/compact-library/`.
3. From the repository root, run:

   ```bash
   node scripts/build-plugin-index.cjs
   ```

   You don't need to install anything else. The script checks your manifest with the same rules Ascendara uses and adds your plugin, with a hash of every file, to `plugins/index.json`. Fix any error it reports and run it again.
4. Commit your plugin's folder and `plugins/index.json`. Don't change any other files.
5. Open a pull request against `development`. Use the plugin template by adding `?template=plugin.md` to the pull request URL, or copy [the template](https://github.com/Ascendara/ascendara/blob/development/.github/PULL_REQUEST_TEMPLATE/plugin.md) into the description. It asks for:
   - the plugin's name, version, and what it does
   - which permissions it uses, and why
   - how you tested it
6. Automated checks run on the pull request. They fail if your manifest is invalid or `index.json` doesn't match your files, so make sure you ran the script after your last change.
7. A maintainer reviews the code and may ask for changes. Push them to the same branch.

Once the pull request is merged, the plugin appears in **Browse** for everyone within a few minutes.

## Updating your plugin

1. Change your plugin's files and **increase `version`** in `manifest.json`. See [Versioning](/docs/plugins/manifest#versioning).
2. Run `node scripts/build-plugin-index.cjs` again.
3. Open a pull request against `development` the same way, and list what changed.

After it's merged, users see an **Update** button. Users choose when to update, so make sure a new version works with whatever the previous one saved, such as `storage` values and files in the data folder.

## How the official list works

This section is for maintainers, and for anyone curious about how Ascendara keeps installs safe.

Each plugin is a folder in `plugins/`, named after its id. `plugins/index.json` lists every plugin's manifest, along with each of its files and that file's SHA-256 hash. Ascendara downloads `index.json` from GitHub, and when you install or update a plugin, it downloads each file and checks its hash. If any file doesn't match, the install stops and nothing changes, so users always get exactly the version that was reviewed.

`scripts/build-plugin-index.cjs` regenerates the index. It fails if a plugin is invalid, references a missing file, or is larger than 50 MB. The repository's `Source checks` workflow runs on every pull request, and its tests fail when `index.json` doesn't match the plugin folders, so a stale index can't be merged by accident.

When reviewing a plugin pull request, check that it only touches one plugin's folder and `plugins/index.json`, and read every file.

Ascendara reads the list from the `development` branch, so a plugin is visible as soon as its pull request is merged. Merging a plugin doesn't trigger a new Ascendara build. GitHub and Ascendara each cache the list for about five minutes.

To try the list before merging, [run Ascendara from source](/docs/developer/build-from-source). The **Browse** tab then has a **This checkout** / **GitHub** switch, and **This checkout** reads `plugins/` from your local copy of the repository, hash checks included.

Files in `plugins/` are stored with their exact bytes (see `.gitattributes`), so line-ending conversion on Windows doesn't change their hashes.
