# UberTmux - Multi-Topic Terminal Multiplexer

UberTmux is an enhanced tmux wrapper that provides topic-based session management for organizing your terminal workflows. It creates an isolated tmux environment with Ctrl+A prefix while enabling you to run nested tmux sessions with the standard Ctrl+B prefix.

## Table of Contents

- [Installation](#installation)
- [Quick Start](#quick-start)
- [Topic-Based Workflow](#topic-based-workflow)
- [Command Reference](#command-reference)
- [Configuration](#configuration)
- [Workspace Integration](#workspace-integration)
- [Key Bindings](#key-bindings)
- [Use Cases](#use-cases)
- [Troubleshooting](#troubleshooting)
- [Advanced Usage](#advanced-usage)

## Installation

1. Clone this repository or download the `ubertmux` script
2. Make it executable and place it in your PATH:

```bash
chmod +x ubertmux
mv ubertmux ~/bin/  # or any directory in your PATH
```

3. Ensure tmux is installed on your system:

```bash
# Ubuntu/Debian
sudo apt install tmux

# macOS
brew install tmux

# Fedora/RHEL
sudo dnf install tmux
```

## Quick Start

```bash
# Start default ubertmux session
ubertmux

# Create or switch to a topic session
ubertmux --topic sysadmin
ubertmux --topic development
ubertmux --topic health

# List all active topics
ubertmux --list-topics

# Get help
ubertmux --help
```

## Topic-Based Workflow

UberTmux organizes your work into **topics** - separate tmux sessions for different areas of responsibility:

### Session Organization

- **Default session**: `ubertmux` - Your main workspace
- **Topic sessions**: `ubertmux-<topic>` - Isolated environments for specific purposes

### Example Topics

- `sysadmin` - System administration tasks, log monitoring
- `development` - Programming projects, code editing
- `health` - Health monitoring, personal tracking
- `research` - Documentation, web browsing
- `finance` - Financial tools, calculations

### Session Navigation

Within any ubertmux session, use:

- **Ctrl+A + s** - Show all sessions and switch between topics
- **Ctrl+A + ?** - List all sessions with window counts
- **Ctrl+A + S** - Create a new session

## Command Reference

### Basic Commands

```bash
ubertmux                         # Start/attach to default session
ubertmux --topic <name>         # Switch to or create topic session
ubertmux --list-topics          # List all active topic sessions
ubertmux --new-topic <name>     # Explicitly create new topic session
ubertmux --help                 # Show help message
```

### Topic Naming Rules

Topic names must contain only:

- Letters (a-z, A-Z)
- Numbers (0-9)
- Hyphens (-)
- Underscores (_)

Examples: `sysadmin`, `dev-work`, `health_tracking`, `project-2024`

## Configuration

UberTmux creates `~/.ubertmux.conf` with enhanced settings:

### Key Features

- **Prefix**: Ctrl+A (instead of default Ctrl+B)
- **Status Line**: Shows current session/topic name
- **Mouse Support**: Enabled for easier navigation
- **Enhanced Splits**: `|` for vertical, `-` for horizontal
- **Vi Mode**: For copy/paste operations
- **Session Management**: Improved session switching

### Customization

Edit `~/.ubertmux.conf` to customize:

```bash
# Add custom keybindings
bind-key r source-file ~/.ubertmux.conf \; display "Config reloaded!"

# Customize status line colors
set -g status-style 'fg=green,bg=black'

# Add topic-specific settings
# (Settings apply to all sessions in the ubertmux server)
```

## Workspace Integration

### Global Workspace

Set a default working directory for all ubertmux sessions:

```bash
export UBERTMUX_WORKSPACE="/path/to/your/workspace"
ubertmux  # Will start in the specified directory
```

### Topic-Specific Workspaces

Define different working directories per topic:

```bash
# In your shell configuration (.bashrc, .zshrc, etc.)
export UBERTMUX_WORKSPACE_SYSADMIN="/var/log"
export UBERTMUX_WORKSPACE_DEVELOPMENT="$HOME/projects"
export UBERTMUX_WORKSPACE_HEALTH="$HOME/health-data"

# Now each topic starts in its designated directory
ubertmux --topic sysadmin      # Starts in /var/log
ubertmux --topic development   # Starts in ~/projects
ubertmux --topic health        # Starts in ~/health-data
```

### Environment Variable Pattern

```bash
UBERTMUX_WORKSPACE_<TOPIC_NAME_UPPERCASE>="/path/to/directory"
```

Topic names are converted: `dev-work` → `UBERTMUX_WORKSPACE_DEV_WORK`

## Key Bindings

### Session Management

| Key Combination | Action |
|-----------------|--------|
| Ctrl+A + s | Choose session (topic switching) |
| Ctrl+A + S | Create new session |
| Ctrl+A + ? | List all sessions with info |
| Ctrl+A + d | Detach from session |

### Window Management

| Key Combination | Action |
|-----------------|--------|
| Ctrl+A + c | Create new window |
| Ctrl+A + w | Choose window |
| Ctrl+A + n | Next window |
| Ctrl+A + p | Previous window |

### Pane Management

| Key Combination | Action |
|-----------------|--------|
| Ctrl+A + \| | Split vertically |
| Ctrl+A + - | Split horizontally |
| Ctrl+A + arrow | Navigate panes |

### Copy Mode

| Key Combination | Action |
|-----------------|--------|
| Ctrl+A + [ | Enter copy mode |
| Ctrl+A + ] | Paste |

## Use Cases

### System Administration

```bash
ubertmux --topic sysadmin
# Terminal 1: tail -f /var/log/syslog
# Terminal 2: htop
# Terminal 3: SSH to servers
# Terminal 4: Log analysis scripts
```

### Development Workflow

```bash
ubertmux --topic development
# Terminal 1: Code editor (vim/nvim)
# Terminal 2: Development server
# Terminal 3: Git operations
# Terminal 4: Testing/debugging
```

### Health Monitoring

```bash
ubertmux --topic health
# Terminal 1: Health data entry scripts
# Terminal 2: Analysis tools
# Terminal 3: Data visualization
# Terminal 4: Backup/sync operations
```

### Multi-Project Management

```bash
# Switch contexts seamlessly
ubertmux --topic project-alpha
ubertmux --topic project-beta
ubertmux --topic maintenance

# Each maintains separate:
# - Working directory
# - Command history per pane
# - Window layouts
# - Running processes
```

## Troubleshooting

### Common Issues

**Q: "open terminal failed: not a terminal"**

A: This happens when running ubertmux in non-interactive contexts. UberTmux requires a proper terminal environment.

**Q: Can't see topic sessions in list**

A: Verify the ubertmux server is running:
```bash
tmux -L ubertmux ls
```

**Q: Topic-specific workspace not working**

A: Check environment variable naming:
```bash
echo $UBERTMUX_WORKSPACE_SYSADMIN
# Should show your directory path
```

**Q: Key bindings not working**

A: Ensure you're using Ctrl+A (not Ctrl+B) and verify config:
```bash
cat ~/.ubertmux.conf | grep "set -g prefix"
```

### Server Management

```bash
# Kill all ubertmux sessions
tmux -L ubertmux kill-server

# List all ubertmux sessions
tmux -L ubertmux ls

# Attach to specific session manually
tmux -L ubertmux attach -t ubertmux-sysadmin
```

## Advanced Usage

### Scripted Topic Creation

```bash
#!/bin/bash
# setup-dev-environment.sh

ubertmux --new-topic development

# Wait for session to be created, then configure it
sleep 1

# Create specific windows
tmux -L ubertmux new-window -t ubertmux-development -n "editor"
tmux -L ubertmux new-window -t ubertmux-development -n "server"
tmux -L ubertmux new-window -t ubertmux-development -n "testing"

# Run commands in windows
tmux -L ubertmux send-keys -t ubertmux-development:editor "vim ." Enter
tmux -L ubertmux send-keys -t ubertmux-development:server "npm start" Enter
```

### Integration with External Tools

```bash
# Create wrapper scripts for common workflows
#!/bin/bash
# monitor-logs.sh
export UBERTMUX_WORKSPACE_MONITORING="/var/log"
ubertmux --topic monitoring
```

### Nested Tmux Sessions

Within any ubertmux topic, you can run regular tmux:

```bash
# Inside ubertmux (Ctrl+A prefix)
tmux new -s inner-session  # Uses Ctrl+B prefix

# Now you have:
# - Outer: ubertmux-topic (Ctrl+A)
# - Inner: inner-session (Ctrl+B)
```

### Auto-start Topics

Add to your shell configuration:

```bash
# Auto-start favorite topics on login
if command -v ubertmux >/dev/null 2>&1; then
    # Start background topics (detached)
    ubertmux --topic sysadmin >/dev/null 2>&1 &
    ubertmux --topic development >/dev/null 2>&1 &
    
    # Attach to main topic
    ubertmux --topic main
fi
```

---

For questions, issues, or contributions, visit the [UberTmux repository](https://github.com/shibuido/ubertmux).