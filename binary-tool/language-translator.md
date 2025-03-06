# Ascendara Language Translator

## Overview
The Language Translator is a command-line tool for translating Ascendara language files. It uses Google Translate's web API to automatically translate English language files into other languages while maintaining the JSON structure and implementing rate limiting for reliable translations.

## Features
- Automated translation of JSON language files
- Progress tracking and reporting with phase monitoring
- Smart rate limiting (8 requests per second) using token bucket algorithm
- Comprehensive error handling with crash reporter integration
- Translation progress persistence with JSON-based state tracking
- Language version management with API synchronization
- Selective key translation for targeted updates
- Breadth-first traversal for predictable translation progress
- Detailed logging system with debug information
- Support for both full translations and partial updates
- Automatic directory creation for output files
- Robust error recovery and reporting

## Usage

### Command Line Arguments
```bash
AscendaraLanguageTranslation.exe [lang] [--output path] [--updateKeys] [--newKey key]
```

### Parameters
- `lang`: Target language code (e.g., fr, de, es)
- `--output`, `-o`: Optional output file path (default: ../languages/{lang}.json)
- `--updateKeys`: Update specific keys in the language file
- `--newKey`: New key to translate (can be specified multiple times)

### Examples
```bash
# Translate entire language file to French
AscendaraLanguageTranslation.exe fr --output ../languages/fr.json

# Update specific keys in Spanish translation
AscendaraLanguageTranslation.exe es --updateKeys --newKey "menu.settings" --newKey "errors.notFound"
```

## Implementation Details

### Rate Limiting
Uses a token bucket algorithm to limit requests to 8 per second:
```python
class RateLimiter:
    def __init__(self, max_per_second):
        self.delay = 1.0 / float(max_per_second)
        self.last_requests = deque(maxlen=max_per_second)
        self.lock = Lock()
        
    def wait(self):
        with self.lock:
            now = time.time()
            # Remove requests older than 1 second
            while self.last_requests and now - self.last_requests[0] >= 1.0:
                self.last_requests.popleft()
            
            # If we haven't hit the limit, proceed immediately
            if len(self.last_requests) < self.last_requests.maxlen:
                self.last_requests.append(now)
                return
            
            # Otherwise, wait until we can make another request
            sleep_time = 1.0 - (now - self.last_requests[0])
            if sleep_time > 0:
                time.sleep(sleep_time)
            self.last_requests.append(time.time())
```

### Progress Tracking
Manages and persists translation progress with phase monitoring:
```python
class TranslationProgress:
    def __init__(self, language_code):
        self.progress_file = os.path.join(os.path.expanduser("~"), 
                                        "translation_progress.ascendara.json")
        self.language_code = language_code
        self.lock = Lock()
        self.total_strings = 0
        self.translated_strings = 0
        self.current_phase = "initializing"
        self._update_progress()
        
    def set_phase(self, phase):
        """Update the current translation phase"""
        self.current_phase = phase
        self._update_progress()
```

### Translation Process
1. Fetches English translations from Ascendara API
2. Counts and analyzes strings for translation
3. Translates strings while maintaining JSON structure using breadth-first traversal
4. Handles selective key updates for targeted translations
5. Saves translations and updates version information via API
6. Integrates with crash reporting system for error handling

### Google Translate Integration
Uses Google Translate's web API with proper headers and token generation:
```python
def translate_text(text, target_lang):
    """Translate text using Google Translate web API"""
    if not text.strip():
        return text

    url = 'https://translate.google.com/translate_a/single'
    params = {
        'client': 'gtx',
        'sl': 'en',
        'tl': target_lang,
        'hl': target_lang,
        'dt': ['at', 'bd', 'ex', 'ld', 'md', 'qca', 'rw', 'rm', 'ss', 't'],
        'ie': 'UTF-8',
        'oe': 'UTF-8',
        'otf': 1,
        'ssel': 0,
        'tsel': 0,
        'kc': 7,
        'q': text
    }
```

### Configuration
Progress and version data are stored in the user's home directory:
```python
progress_file = os.path.join(os.path.expanduser("~"), 
                           "translation_progress.ascendara.json")
timestamp_file = os.path.join(os.environ['USERPROFILE'], 
                            "timestamp.ascendara.json")
```

### Error Handling
Comprehensive error handling with crash reporter integration:
```python
def launch_crash_reporter(error_code, error_message):
    """Register the crash reporter to launch on exit with the given error details"""
    if not hasattr(launch_crash_reporter, "_registered"):
        atexit.register(_launch_crash_reporter_on_exit, error_code, error_message)
        launch_crash_reporter._registered = True
```