# Understanding Error Codes

When one of Ascendara's helper tools fails, it can open the **Crash Reporter**, a small window that shows which component failed and why. This page explains the error codes and what you can do.

## The Crash Reporter

### What It Shows
- **Affected Component**: The tool that failed, such as the Downloader or Game Handler
- **Diagnostic Code**: A numeric error code
- **What Happened**: A description of the code
- **Technical Details**: The error message from the tool

Most of Ascendara's tools currently report the generic code `1`, which the Crash Reporter shows as **Unrecognized error code**. In that case, the **Technical Details** contain the actual error message, so include them when you ask for help.

### Buttons
- **Get Support**: Opens the Ascendara Discord, where you can get help.
- **Upload Crash Report**: Shows a confirmation. The Crash Reporter doesn't send your report or logs to Ascendara; to share your logs with support, use **Upload Logs** with a support code in **Settings**.
- **Close**: Closes the window.

For errors in Ascendara itself rather than a helper, the Crash Reporter has a **Critical Error** layout with recovery suggestions and **Report Problem**, **Restart Ascendara**, and **Exit** buttons. **Restart Ascendara** asks you to reopen Ascendara manually.

## Error Code Categories

### General Errors (1000-1004)
- **1000**: An unknown error occurred
- **1001**: An unhandled exception occurred
- **1002**: An unhandled rejection occurred
- **1003**: A network error occurred
- **1004**: Invalid data received

### Game Handler Errors (1100-1105)
- **1100**: Game not found
- **1101**: Failed to launch game
- **1102**: Game configuration error
- **1103**: Game process error
- **1104**: Settings file error
- **1105**: Download directory error

### Language Translation Errors (1200-1202)
- **1200**: Language Translation API error
- **1201**: Language Translation rate limit exceeded
- **1202**: Error processing the language file

### GoFile Helper Errors (1300-1305)
These codes belong to the former GoFile Helper. Gofile support is now built into the [Downloader](/docs/binary-tool/downloader), so you shouldn't see them.
- **1300**: GoFile API error
- **1301**: Failed to upload file to GoFile
- **1302**: Failed to download file from GoFile
- **1303**: GoFile authentication failed
- **1304**: GoFile rate limit exceeded
- **1305**: Error processing file for GoFile operation

### Downloader Errors (1400-1408)
- **1400**: Failed to initialize download
- **1401**: Error updating download progress
- **1402**: Failed to cancel download
- **1403**: Download verification failed
- **1404**: Failed to extract downloaded files
- **1405**: Error during cleanup
- **1406**: Error reading or writing the settings file
- **1407**: Error reading or writing the games file
- **1408**: Failed to launch helper process

### Torrent Handler Errors (1500-1506)
- **1500**: Torrent API error
- **1501**: Failed to add torrent
- **1502**: Failed to remove torrent
- **1503**: Failed to get torrent status
- **1504**: Torrent configuration error
- **1505**: Error processing torrent file
- **1506**: Failed to install torrent content

### Notification Helper Errors (1600-1604)
- **1600**: Failed to initialize notification
- **1601**: Failed to display notification
- **1602**: Invalid or unsupported theme
- **1603**: Failed to load notification resources
- **1604**: Error during notification animation

## Download Errors in the App

Most download problems don't open the Crash Reporter. Instead, the download card on the **Downloads** page shows the error. See [Common Issues](/docs/troubleshooting/common-issues#download-issues) for each message and its fix.

## Recovery Steps
1. Read the **Technical Details** for the actual error message.
2. Try the action again. Many errors are temporary network problems.
3. Run the health check in the [System Center](/docs/features/system-center).
4. Check [Common Issues](/docs/troubleshooting/common-issues) for known problems.
5. If the problem continues, ask for help in the [Discord community](https://ascendara.app/discord) and include the component name, code, and technical details.

For developers, see the [Crash Reporter](/docs/binary-tool/crash-reporter) documentation.
