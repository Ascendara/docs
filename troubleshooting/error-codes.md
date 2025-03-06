# Understanding Error Codes

## Error Code Categories

### General Errors (1000-1004)
- **1000**: Unknown Error
- **1001**: Unhandled Exception
- **1002**: Unhandled Reject
- **1003**: Network Error
- **1004**: Invalid Data

### Game Handler Errors (1100-1199)
- **1100**: Game Not Found
- **1101**: Game Launch Failed
- **1102**: Game Process Error
- **1103**: Game Configuration Error
- **1104**: Settings Error
- **1105**: Download Directory Error

### GoFile Helper Errors (1200-1299)
- **1200**: GoFile API Error
- **1201**: GoFile Upload Error
- **1202**: GoFile Download Error
- **1203**: GoFile Authentication Error
- **1204**: GoFile Rate Limit
- **1205**: GoFile File Error

### Main Downloader Errors (1300-1399)
- **1300**: Download Initialization Error
- **1301**: Download Progress Error
- **1302**: Download Cancel Error
- **1303**: Download Verification Error
- **1304**: Download Extraction Error
- **1305**: Download Cleanup Error
- **1306**: Settings File Error
- **1307**: Games File Error
- **1308**: Helper Launch Error

## The Crash Reporter

### Overview
Ascendara includes a built-in crash reporter tool that helps diagnose and report issues when something goes wrong. When an error occurs, the crash reporter will automatically launch and provide detailed information about the problem.

### What Information is Collected
The crash reporter collects:
- Error code and description
- Affected component name
- Technical details about the error
- System information relevant to the crash

### Using the Crash Reporter

#### For Normal Errors
1. When a utility crashes, you'll see the "Ascendara Core Utility Crash" window
2. The window shows:
   - Affected component name
   - Diagnostic code
   - Error description
   - Technical details
3. You can:
   - Get support through the Ascendara help center
   - Upload the crash report
   - Close the window

#### For Critical Errors
1. If Ascendara itself crashes, you'll see the "Critical Error" window
2. The window provides:
   - Error code and details
   - Recovery suggestions
   - Technical information
3. Options available:
   - Report the problem
   - Restart Ascendara
   - Exit the application

### Reporting Issues
1. Click "Upload Crash Report" to send the report to the Ascendara team
2. The report helps Ascendara:
   - Identify the root cause
   - Improve stability
   - Fix bugs faster
3. Reports are anonymous and only include technical information

### Recovery Steps
1. Try the suggested solutions in the crash reporter
2. Restart Ascendara if recommended
3. Check the Ascendara support center for known issues
4. Contact support if the problem persists