# Importing the Extension from Source Code

Importing the Ascendara Download Handler extension from source code is simple and easy to follow, but is not recommended for most users. Install the extension from [here](https://ascendara.app/extension)  instead.

## Prerequisites
- A supported browser (Chrome, Firefox, Edge, Opera, or Brave)
- Git (optional, for cloning the repository)

## Installation Steps

### Step 1: Get the Source Code
You have two options:

#### Option A: Download ZIP (Easiest)
1. Visit the [GitHub repository](https://github.com/t-a-g-o/ascendaradownloadhandler)
2. Click the green "Code" button
3. Select "Download ZIP"
4. Extract the ZIP file to a location you'll remember

#### Option B: Clone Repository (For Developers)
If you're familiar with Git, you can clone the repository:
```
git clone https://github.com/t-a-g-o/ascendaradownloadhandler
```

### Step 2: Configure Manifest

Due to differences in browser requirements, you'll need to merge the appropriate manifest file content:

1. In the extension folder, you'll find two manifest files:
   - `manifest.chrome.json`
    For Chrome-based browsers (Chrome, Edge, Opera, Brave)
   - `manifest.firefox.json`
    For Firefox

2. Based on your browser:
   - For Chrome-based browsers: Copy the contents from `manifest.chrome.json` into `manifest.json`
   - For Firefox: Copy the contents from `manifest.firefox.json` into `manifest.json`

This step is necessary because each browser has different manifest requirements that can conflict with each other.

### Step 3: Load the Extension

#### For Chrome, Edge, Opera, or Brave:
1. Open your browser's extension management page:
   - Chrome: `chrome://extensions`
   - Edge: `edge://extensions`
   - Opera: `opera://extensions`
   - Brave: `brave://extensions`
2. Enable "Developer mode" using the toggle in the top-right corner
3. Click "Load unpacked" in the top-left corner
4. Navigate to the extracted/cloned extension folder and select it
5. The extension should now appear in your browser

#### For Firefox:
1. Open Firefox and navigate to `about:debugging`
2. Click "This Firefox" in the left sidebar
3. Click "Load Temporary Add-on"
4. Navigate to the extracted/cloned extension folder
5. Select the "manifest.json" file

### Step 4: Pin the Extension
1. Click the puzzle piece icon in your browser's toolbar
2. Find the Ascendara extension
3. Click the pin icon to keep it easily accessible

## Usage Guide

1. Once installed, the extension will automatically detect compatible downloads
2. When a download starts, you'll see a prompt to open Ascendara
3. Click "Open in Ascendara" to redirect the download
4. A new tab will open and the download will start automatically

## Troubleshooting

- If the extension doesn't load, ensure you selected the correct directory containing the "manifest.json" file
- For Firefox users: Note that temporary add-ons will need to be reloaded after restarting the browser
- Make sure you have the latest version of your browser installed

## Recommended Browser

Ascendara recommends using [Arc Browser](https://arc.net/download) for the best experience. Arc includes built-in ad and popup blocking features that enhance your browsing experience while using Ascendara.

## Need Help?

If you encounter any issues during installation, please:
1. Check the [Troubleshooting Guide](/docs/troubleshooting/common-issues)
2. Visit the [GitHub Issues](https://github.com/t-a-g-o/ascendaradownloadhandler/issues) page
3. Join the [Discord Community](https://ascendara.app/discord) for real-time support