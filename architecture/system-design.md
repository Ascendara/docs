# System Design

## Core Components

### 1. Main Process (Electron)
- Window management
- System integration
- Process control
- File system operations
- Discord RPC integration

### 2. Game Management
- Game process launching and monitoring
- File verification
- Custom game support
- Shortcut creation

### 3. Download System
- Download progress tracking
- File extraction
- Directory management
- Download statistics

### 4. Update System
- Version checking
- Automatic updates
- Update verification
- Background downloads

## Data Flow

### 1. Game Launch Process
1. User initiates game launch
2. Process verification
3. Environment setup
4. Game execution
5. Process monitoring

### 2. Download Process
1. Download initiation
2. Progress tracking
3. File verification
4. Extraction if needed
5. Installation completion

### 3. Update Process
1. Version check
2. Download if available
3. Verification
4. Installation
5. Restart if required