# Building Ascendara from Source

## Prerequisites

Before building Ascendara, ensure you have the following installed:

- **Node.js**
  - Version 18 or higher recommended
  - Required for yarn and JavaScript runtime
- **Windows Build Tools**
  - Visual Studio Build Tools with Windows SDK
  - Required for native dependencies (diskusage and electron-related builds)

While not required, the following tools can make the development experience easier:

- **Git**: Optional - You can either clone the repository or simply download the files directly from GitHub
- **Python 3.x**: Optional - Provides automated build scripts to simplify the development process, but all steps can be performed manually


## Important Limitations

When running from source, Ascendara will run in Development Mode. Please be aware of the following limitations 
while in Development Mode, or while using a public build:

- **API Features**: 
  - Feature reporting
  - Game reporting
  - Download reporting
- **Analytics Service**: The analytics service will be completely non-functional in the public version
- **Game Execution**: You will not be able to run games in development mode

These limitations exist to protect Ascendara's infrastructure from potential abuse. The reporting and analytics services are essential for monitoring health, detecting issues, and ensuring Ascendara stays secure. For these reasons, these features are restricted to official builds only. You can still run games off the public version of Ascendara you just have to be running the built version. Thank you for your understanding.

## Getting the Source Code

1. Clone the repository:
```bash
git clone https://github.com/ascendara/Ascendara.git
```

2. Install dependencies:
```bash
yarn
```

3. Install Python requirements:
```bash
pip install -r requirements.txt
```

## Development Preview

To run a live developer preview:

1. Open the directory in VSCode or command prompt
2. In "electron/app.js", set `isDev = true`
3. Run the start script:
```bash
yarn start
```
This will concurrently run electron and Vite.

## Building to EXE

### Automatic Build (Recommended)

The simplest way to build Ascendara is using the distribution command:

```bash
yarn dist
```

This will:
1. Execute the build process via execute.py
2. Build the React app
3. Build the Electron app
4. Output files to the `dist/` directory

### Manual Build Process

If you need more control, you can build manually:

1. Build the React app:
```bash
yarn build
```

2. After building, the React app will be in the "build" directory:
   - Modify "index.html" to use relative paths by replacing "/assets/" with "assets/"
   - Copy "index.html" to the "electron" directory
   - Copy the "assets" directory to the "electron" directory
   - Copy any other required assets (guide directory, icon.png, etc.) to the "electron" directory

3. Modify "electron/app.js" to:
   - Make sure isDev = false at the top of the file
   - Ensure it's configured to load the index.html from the correct location:
   `mainWindow.loadURL('file://' + path.join(__dirname, 'index.html'))`

4. Build the Electron app:
```bash
yarn buildwithelectron
```

5. Clean up temporary files:
   - Remove the "build" directory
   - Remove the copied files from the "electron" directory (index.html, assets, guide, etc.)

Your compiled executable will be available in the "dist/win-unpacked" directory.