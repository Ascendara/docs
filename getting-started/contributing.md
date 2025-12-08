# Contributing to Ascendara

Thank you for your interest in contributing to Ascendara! This guide will help you get started.

## Getting Started

### Prerequisites

- **Node.js** 18+ 
- **Python** 3.8+
- **Git**
- **Visual Studio Build Tools** (Windows, for native dependencies)

### Setting Up Your Environment

1. **Fork the repository** on GitHub

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

4. **Start the development server**:
   ```bash
   yarn start
   ```

---

## Code Style

### JavaScript/React

- Use **ES6+** syntax
- Use **functional components** with hooks
- Follow existing naming conventions:
  - Components: `PascalCase`
  - Functions/variables: `camelCase`
  - Constants: `UPPER_SNAKE_CASE`
- Use **async/await** over `.then()` chains

### IPC Handlers

- Use `_` for unused parameters:
  ```javascript
  // Good
  ipcMain.handle("get-data", async (_, id) => { ... });
  
  // Only use event when needed
  ipcMain.handle("send-data", async (event, data) => {
    event.sender.send("data-received", result);
  });
  ```

- Group related handlers in appropriate modules
- Add JSDoc comments for complex handlers

### Python

- Follow **PEP 8** style guidelines
- Use **type hints** where possible
- Document functions with docstrings

### CSS/Styling

- Use **TailwindCSS** utility classes
- Avoid custom CSS unless necessary
- Follow the existing color scheme and design patterns

---

## Making Changes

### Branch Naming

Use descriptive branch names:
- `feature/add-game-sorting`
- `fix/download-progress-bug`
- `docs/update-api-reference`
- `refactor/settings-module`

### Commit Messages

Follow conventional commit format:
```
type(scope): subject

# Examples:
feat(downloads): add pause/resume functionality
fix(games): resolve shortcut creation on Windows 11
docs(api): update endpoint documentation
refactor(settings): extract validation logic
```

Types:
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `refactor`: Code refactoring
- `style`: Formatting changes
- `test`: Adding tests
- `chore`: Maintenance tasks

### Pull Request Process

1. **Create a feature branch** from `main`
2. **Make your changes** with clear commits
3. **Test thoroughly**:
   - Run the app in development mode
   - Test on Windows (primary platform)
   - Check for console errors
4. **Update documentation** if needed
5. **Submit a pull request** with:
   - Clear description of changes
   - Screenshots for UI changes
   - Reference to related issues

---

## Development Guidelines

### Adding New IPC Handlers

1. **Choose the right module** in `electron/modules/`
2. **Add the handler**:
   ```javascript
   // In the appropriate module
   ipcMain.handle("new-handler", async (_, param1, param2) => {
     try {
       // Implementation
       return { success: true, data: result };
     } catch (error) {
       console.error("Error in new-handler:", error);
       return { success: false, error: error.message };
     }
   });
   ```

3. **Expose in preload.js**:
   ```javascript
   // Add to the appropriate section
   newHandler: (param1, param2) => ipcRenderer.invoke("new-handler", param1, param2),
   ```

4. **Document in IPC Reference** if it's a public API

### Adding New Pages

1. Create the component in `src/pages/`
2. Add the route in `App.jsx`
3. Add navigation in `Navbar.jsx` if needed
4. Follow existing page patterns for consistency

### Modifying Python Tools

1. Make changes in `binaries/{tool}/src/`
2. Test with Python directly first:
   ```bash
   python src/AscendaraDownloader.py --help
   ```
3. Build the executable:
   ```bash
   pyinstaller --noconsole --onefile src/AscendaraDownloader.py
   ```
4. Test the built executable

---

## Testing

### Manual Testing Checklist

Before submitting a PR, verify:

- [ ] App starts without errors
- [ ] No console errors in DevTools
- [ ] Feature works as expected
- [ ] Existing features still work
- [ ] UI looks correct (light/dark themes)
- [ ] Settings persist after restart

### Testing Downloads

> **Note**: Download testing requires a built version, not dev mode.

1. Build the app: `yarn dist`
2. Run from `dist/win-unpacked/`
3. Test download flows

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
- Translate to other languages

---

## Getting Help

- **Discord**: [ascendara.app/discord](https://ascendara.app/discord) - `#development` channel
- **GitHub Discussions**: For longer technical discussions
- **GitHub Issues**: For bug reports and feature requests

---

## Code of Conduct

- Be respectful and inclusive
- Provide constructive feedback
- Help others learn and grow
- Focus on the code, not the person

---

## License

By contributing to Ascendara, you agree that your contributions will be licensed under the same license as the project.

---

Thank you for contributing to Ascendara!
