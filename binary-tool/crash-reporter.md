# Ascendara Crash Reporter

## Overview
The Crash Reporter is a small window that shows which Ascendara component failed, an error code, and the error message. Helper tools open it when they hit an unhandled error. It's written in Rust with the [FLTK](https://www.fltk.org/) GUI toolkit (through the `fltk` and `fltk-theme` crates), replacing the earlier Python and tkinter version.

Source: `binaries/AscendaraCrashReporter/src/main.rs`

For what users see and what the codes mean, see [Error Codes](/docs/troubleshooting/error-codes).

## Features
- Two layouts: a **critical** layout for errors in Ascendara itself, and a **utility** layout for helper tools
- Component names and descriptions for known error codes
- Technical details from the caller
- A **Get Support** button that opens the Ascendara Discord
- Loads `ascendara.ico` from its own folder as the window icon

## Usage

### Command Line Arguments
```bash
AscendaraCrashReporter <tool_id> <error_code> <error_message>
```

All three arguments are required. If any is missing, or the code isn't a number, the reporter exits without showing a window.

### Parameters
| Argument | Description |
| --- | --- |
| `tool_id` | The component that failed (case-insensitive). See [Tool Identifiers](#tool-identifiers). |
| `error_code` | A numeric error code. Unknown codes are shown as **Unrecognized error code**. |
| `error_message` | The error message, shown as the technical details |

### Example
```bash
AscendaraCrashReporter.exe maindownloader 1404 "Failed to extract archive: CRC error"
```

## Development

### Tool Identifiers
| ID | Shown as | Layout |
| --- | --- | --- |
| `toplevel` | Ascendara | Critical |
| `maindownloader` | Ascendara Downloader | Utility |
| `gamehandler` | Ascendara Game Handler | Utility |
| `languagetranslation` | Ascendara Language Translation | Utility |
| `torrenthandler` | Ascendara Torrent Handler | Utility |
| `notificationhelper` | Ascendara Notification Helper | Utility |
| `gofilehelper` | Ascendara GoFile Helper (legacy) | Utility |

Any other ID is shown as **Unknown Ascendara Tool**.

### Error Code Ranges
| Range | Component |
| --- | --- |
| 1000–1004 | General |
| 1100–1105 | Game Handler |
| 1200–1202 | Language Translation |
| 1300–1305 | GoFile Helper (legacy) |
| 1400–1408 | Downloader |
| 1500–1506 | Torrent Handler |
| 1600–1604 | Notification Helper |

The individual codes are listed in [Error Codes](/docs/troubleshooting/error-codes#error-code-categories) and defined in `ErrorCodes::get_error_description` in `main.rs`.

> **Note:** The Python helpers currently pass the generic code `1`, which is shown as **Unrecognized error code**, so the message argument carries the useful information. Pass a specific code from the table when you add new error handling.

### Layouts

#### Critical (`toplevel`)
An 800×600 window with a light red background, titled **⚠ Critical Error: Ascendara Has Stopped Working**. It shows the code, its description, recovery suggestions, and technical information, with **Report Problem**, **Restart Ascendara**, and **Exit** buttons.

#### Utility (all other tools)
A 600×550 window titled **⚠️ Ascendara Core Utility Crash**. It shows the **Affected Component**, **Diagnostic Code**, **What Happened**, and **Technical Details**, with **Get Support**, **Upload Crash Report**, and **Close** buttons.

### Current Limitations
- **Upload Crash Report** and **Report Problem** only show a confirmation dialog. No data is sent anywhere. Users who need help should use **Upload Logs** with a support code in Ascendara's Settings.
- **Restart Ascendara** asks the user to restart Ascendara manually.
- Ascendara's main process calls the reporter with only two arguments (an error type and a message) for uncaught exceptions, so the reporter exits without showing the critical layout.

### Integration
Python helpers register the reporter to open when the process exits, so the error is shown after the tool has cleaned up:

```python
def _launch_crash_reporter_on_exit(error_code, error_message):
    binary_name = 'AscendaraCrashReporter.exe' if sys.platform == 'win32' else 'AscendaraCrashReporter'
    # Packaged: next to the helper. Source: binaries/AscendaraCrashReporter/target/release
    crash_reporter_path = os.path.join(os.path.dirname(sys.executable), binary_name)
    if os.path.exists(crash_reporter_path):
        kwargs = {"creationflags": subprocess.CREATE_NO_WINDOW} if sys.platform == "win32" else {}
        subprocess.Popen([crash_reporter_path, "your_tool_id", str(error_code), error_message], **kwargs)

def launch_crash_reporter(error_code, error_message):
    if not hasattr(launch_crash_reporter, "_registered"):
        atexit.register(_launch_crash_reporter_on_exit, error_code, error_message)
        launch_crash_reporter._registered = True
```

## Building
```bash
cd binaries/AscendaraCrashReporter
cargo build --release --locked
```

The executable is written to `target/release/AscendaraCrashReporter(.exe)`, which is where `package.json` expects it. On Windows, `build.rs` uses `winres` to embed the icon. On macOS, the build script passes an explicit Rust target for each architecture. See [Creating the Tools](/docs/developer/creating-the-tools).
