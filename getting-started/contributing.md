# Contributing to Ascendara

Thank you for your interest in contributing to Ascendara! This guide covers how to set up the project, the conventions the codebase follows, and how to submit changes. Accepted contributions are credited in the Ascendara changelog.

## Getting Started

### Prerequisites

- **Node.js** 22 (CI uses 22.14.0) and **Yarn** 1.x
- **Python** 3.12 (for the helper tools)
- **Git**
- **Rust and Cargo** (only if you work on or build the Crash Reporter)
- **Visual Studio Build Tools** with the Windows SDK (Windows, for native dependencies)

See [Building from Source](/docs/developer/build-from-source) for the full setup, including optional Firebase configuration.

### Setting Up Your Environment

1. **Fork the repository** on GitHub.

2. **Clone your fork**:
   ```bash
   git clone https://github.com/YOUR_USERNAME/ascendara.git
   cd ascendara
   ```

3. **Install dependencies**:
   ```bash
   yarn install
   pip install -r requirements.txt
   ```

4. **Start the development app**:
   ```bash
   yarn start
   ```

`yarn install` also sets up a Husky pre-commit hook that runs Prettier on staged files through lint-staged.

---

## Code Style

### Formatting and Linting

- **Prettier** formats JavaScript, JSX, JSON, Markdown, HTML, and CSS: 2-space indentation, double quotes, a 90-character line width, and sorted Tailwind classes. Translation files in `src/translations/` are key-sorted automatically.
- **ESLint** checks `src`, `electron`, the achievement watcher, and scripts.

```bash
yarn format        # format everything
yarn format:check  # check formatting without writing
yarn lint          # run ESLint
```

### JavaScript/React

- Use functional components with hooks.
- Follow the existing naming conventions:
  - Components: `PascalCase`
  - Functions and variables: `camelCase`
  - Constants: `UPPER_SNAKE_CASE`
- Prefer `async`/`await` over `.then()` chains.
- Put user-facing text in `src/translations/en.json` and read it with `t("section.key")`. Don't hardcode UI strings.

### Python

- Follow **PEP 8**.
- Write JSON state files atomically, as the existing tools do, because the app reads them while the tools run.
- Keep platform-specific paths for Windows, macOS, and Linux in sync with the other tools.

### CSS/Styling

- Use **Tailwind CSS** utility classes and the existing shadcn/ui components in `src/components/ui/`.
- Use theme color tokens (such as `bg-card`, `text-primary`, `text-muted-foreground`) instead of fixed colors so every theme works.

---

## Making Changes

### Branches

Active development happens on the `development` branch, which is merged into `main` for releases. Use a descriptive branch name for your work:
- `feature/add-game-sorting`
- `fix/download-progress-bug`
- `docs/update-api-reference`

### Commit Messages

Use the conventional commit format:
```
type(scope): subject

# Examples:
feat(big-picture): offer to start steam before launching online-fix games
fix(index): only ask for help when the shared index is stale
ci: write release descriptions from the changelog
```

Types:
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `refactor`: Code refactoring
- `style`: Formatting changes
- `test`: Adding or changing tests
- `ci`: Build and release automation
- `chore`: Maintenance tasks

### Pull Request Process

1. **Create a feature branch.**
2. **Make your changes** with clear commits.
3. **Check your work**:
   - Run `yarn lint` and `yarn build`
   - Run the app with `yarn start` and check the DevTools console for errors
   - Test on Windows (the primary platform), and on Linux or macOS if your change affects them
4. **Update the documentation** if needed. The docs live in the [Ascendara docs repository](https://github.com/Ascendara/docs).
5. **Submit a pull request** with:
   - A clear description of the change
   - Screenshots for UI changes
   - References to related issues

Pull requests to `main` run the **Source checks** workflow, which installs dependencies and builds the renderer.

---

## Development Guidelines

### Adding New IPC Handlers

1. **Choose the right module** in `electron/modules/`, or create one and register it from `electron/app.js`.
2. **Add the handler**:
   ```javascript
   ipcMain.handle("new-handler", async (_, param1, param2) => {
     try {
       return { success: true, data: result };
     } catch (error) {
       console.error("Error in new-handler:", error);
       return { success: false, error: error.message };
     }
   });
   ```
   Use `_` for the event parameter when you don't need it.
3. **Expose it in `electron/preload.js`**:
   ```javascript
   newHandler: (param1, param2) => ipcRenderer.invoke("new-handler", param1, param2),
   ```
4. **Call it from React** with `window.electron.newHandler(...)`.
5. **Document it** in the [IPC Reference](/docs/developer/ipc-reference).

Every `ipcMain.handle` registered after startup is wrapped by `electron/modules/security.js`, which rejects calls that don't come from Ascendara's own window. Don't add handlers that accept arbitrary file paths or URLs without validating them. Prefer a named preload API over the legacy `window.electron.ipcRenderer` bridge.

### Adding New Pages

1. Create the component in `src/pages/`.
2. Add the route in `src/app.jsx`.
3. Add navigation in `src/components/Navigation.jsx` if the page needs a navigation item.
4. Add any searchable settings to `src/lib/settingsSearchData.js`.
5. Follow existing page patterns for consistency.

### Adding Translations

Add new keys to `src/translations/en.json` only. The other built-in languages are generated from the English file with machine translation, so manual edits to them are overwritten. See [Language Translator](/docs/binary-tool/language-translator).

### Modifying the Helper Tools

1. Make changes in `binaries/{tool}/src/`.
2. Test with Python directly first:
   ```bash
   python binaries/AscendaraDownloader/src/AscendaraDownloader.py --help
   ```
3. Build the executable with the platform build script. See [Creating the Tools](/docs/developer/creating-the-tools).
4. Test the built executable from a packaged build.

---

## Testing

### Automated Checks

```bash
yarn lint
yarn build
python -m unittest discover -s tests   # Python tests for build and release scripts
```

### Manual Testing Checklist

Before submitting a PR, verify:

- [ ] The app starts without errors
- [ ] There are no new console errors in DevTools
- [ ] Your feature works as expected
- [ ] Existing features still work
- [ ] The UI looks correct in light and dark themes
- [ ] Settings persist after a restart

### Testing Downloads and Game Launches

> **Note**: Games can't be launched in development mode, and some API features are unavailable in source builds. Use a packaged build to test these flows.

Build the app (see [Building from Source](/docs/developer/build-from-source)), then run it from `dist/win-unpacked/` on Windows, or from the AppImage or `.app` on Linux and macOS.

---

## Areas for Contribution

### Good First Issues

Look for issues labeled `good first issue` on GitHub. These are typically:
- Documentation improvements
- Minor UI tweaks
- Bug fixes with clear reproduction steps

### Feature Requests

Check the [roadmap](https://ascendara.app/roadmap) and GitHub issues for planned features. Comment on an issue before starting work to avoid duplicates.

### Documentation

Documentation improvements are always welcome:
- Fix typos or unclear explanations
- Add examples
- Update outdated information

---

## Getting Help

- **Discord**: [ascendara.app/discord](https://ascendara.app/discord)
- **GitHub Issues**: For bug reports and feature requests

---

## Code of Conduct

Please follow the [Code of Conduct](https://github.com/Ascendara/ascendara/blob/main/CODE_OF_CONDUCT.md):

- Be respectful and inclusive
- Provide constructive feedback
- Help others learn and grow
- Focus on the code, not the person

---

## License

By contributing to Ascendara, you agree that your contributions will be licensed under the same license as the project.

---

Thank you for contributing to Ascendara!
