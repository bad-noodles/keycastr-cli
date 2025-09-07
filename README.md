# KeyCastr CLI

A command-line interface for controlling [KeyCastr](https://github.com/keycastr/keycastr). This tool allows you to toggle, start, stop, and check the status of KeyCastr's keystroke capture functionality directly from the terminal.

## Overview

KeyCastr is a popular macOS application that displays your keystrokes on screen, making it ideal for presentations, tutorials, and screen recordings. This CLI tool provides programmatic control over KeyCastr's capture state through AppleScript automation.

## Features

- **Toggle capture**: Switch between capturing and not capturing keystrokes (default command)
- **Status check**: Get the current capture state of KeyCastr
- **Start capture**: Begin keystroke capture if not already active
- **Stop capture**: Stop keystroke capture if currently active
- **Open application**: Launch KeyCastr if not already running
- **Close application**: Quit KeyCastr gracefully or force close if needed
- **Running check**: Verify if KeyCastr process is currently active
- **Zsh completions**: Full tab completion support for all commands

## Prerequisites

- macOS
- [KeyCastr](https://github.com/keycastr/keycastr) installed and accessible

## Installation

```bash
brew install bad-noodles/bad-noodles/keycastr-cli
```

## Usage

### Basic Commands

```bash
# Toggle capture state (default behavior)
keycastr-cli
keycastr-cli toggle

# Check current status
keycastr-cli status

# Start capturing (if not already active)
keycastr-cli start

# Stop capturing (if currently active)
keycastr-cli stop

# Open KeyCastr application
keycastr-cli open

# Close KeyCastr application
keycastr-cli close

# Check if KeyCastr is running
keycastr-cli running

# Show help
keycastr-cli help
```

### Command Details

| Command | Description | Output |
|---------|-------------|---------|
| `toggle` | Toggles between capturing/not capturing | `Toggled KeyCastr capture state` |
| `status` | Shows current capture state | `capturing`, `stopped`, or `unknown` |
| `start` | Starts capture if stopped | `Toggled KeyCastr capture state` or `KeyCastr is already capturing` |
| `stop` | Stops capture if active | `Toggled KeyCastr capture state` or `KeyCastr is already stopped` |
| `open` | Opens KeyCastr application | `Opened KeyCastr` or `KeyCastr is already running` |
| `close` | Closes KeyCastr application | `Closed KeyCastr` or `KeyCastr is not running` |
| `running` | Checks if KeyCastr is running | `KeyCastr is running` or `KeyCastr is not running` |
| `install-completions` | Outputs zsh completion script | Completion script text |
| `help` | Shows usage information | Help text |

### Example Workflows

#### Complete Workflow - From Closed to Recording

```bash
# Start from scratch - ensure KeyCastr is open and capturing
keycastr-cli open      # Launch KeyCastr if not running
keycastr-cli start     # Begin keystroke capture

# ... do your screen recording ...

# Clean shutdown
keycastr-cli stop      # Stop capturing
keycastr-cli close     # Close KeyCastr completely
```

#### Quick Status Check

```bash
# Check everything at once
if keycastr-cli running; then
    echo "KeyCastr status: $(keycastr-cli status)"
else
    echo "KeyCastr is not running"
fi
```

#### Automated Setup Script

```bash
#!/bin/bash
# presentation-setup.sh

echo "Setting up presentation environment..."

# Ensure KeyCastr is running
if ! keycastr-cli running; then
    echo "Opening KeyCastr..."
    keycastr-cli open
fi

# Start capturing
if keycastr-cli start; then
    echo "✓ KeyCastr capture enabled"
else
    echo "✗ Failed to start KeyCastr"
    exit 1
fi

# Your other setup commands here...
```

#### Cleanup Script

```bash
#!/bin/bash
# presentation-cleanup.sh

echo "Cleaning up presentation environment..."

if keycastr-cli running; then
    keycastr-cli stop
    keycastr-cli close
    echo "✓ KeyCastr stopped and closed"
else
    echo "KeyCastr was not running"
fi
```

## How It Works

The CLI tool uses AppleScript to interact with KeyCastr's menu bar interface:

1. **Process Detection**: Checks if KeyCastr is running using `pgrep`
2. **Status Detection**: Reads the menu item text to determine current state
3. **State Control**: Clicks the appropriate menu item to toggle capture state

The tool specifically targets:

- Menu bar item 1 of menu bar 2 (KeyCastr's menu bar icon)
- Menu item 3 (the "Start/Stop Casting" toggle)

## Troubleshooting

### Common Issues

**"Error: KeyCastr is not running"**

- Ensure KeyCastr is launched and running
- Check that the process name matches exactly ("KeyCastr")

**"Error: Failed to toggle KeyCastr"**

- KeyCastr might be in an unexpected state
- Try manually clicking the KeyCastr menu to reset it
- Ensure your system allows AppleScript access to System Events

**Status shows "unknown"**

- KeyCastr's menu structure might have changed
- The menu item text might be different than expected
- Try restarting KeyCastr

### Permissions

On macOS Mojave and later, you might need to grant accessibility permissions:

1. Go to System Preferences → Security & Privacy → Privacy
2. Select "Accessibility" from the left sidebar  
3. Add Terminal (or your terminal application) to the list
4. Ensure it's checked/enabled

## Development

### File Structure

```
keycastr-cli/
├── keycastr-cli           # Main executable script
├── _keycastr-cli          # Zsh completion definitions
└── README.md             # This file
```

### Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly on macOS
5. Submit a pull request

### Testing

Test the tool with different KeyCastr states:

```bash
# Test each command
keycastr-cli status
keycastr-cli toggle
keycastr-cli start
keycastr-cli stop
keycastr-cli help

# Test error conditions (with KeyCastr not running)
keycastr-cli status  # Should show error
```

## Related Projects

- [KeyCastr](https://github.com/keycastr/keycastr) - The main application this tool controls
