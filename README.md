# UberTmux

> Terminal multiplexer with topic-based session management

UberTmux enhances tmux with organized, topic-based workflows. Run multiple isolated tmux sessions for different areas of work (sysadmin, development, health monitoring) while maintaining the ability to use nested tmux sessions without conflicts.

## Key Features

- **🎯 Topic-Based Sessions** - Organize work by context (sysadmin, development, etc.)
- **⌨️ Dual Prefix Support** - Ctrl+A for ubertmux, Ctrl+B for nested tmux
- **📁 Workspace Integration** - Topic-specific working directories  
- **🔧 Enhanced Configuration** - Improved status line, keybindings, and navigation
- **🔄 Seamless Switching** - Quick context switching between topics

## Quick Start

```bash
# Install
chmod +x ubertmux && mv ubertmux ~/bin/

# Basic usage
ubertmux                         # Default session
ubertmux --topic sysadmin       # Sysadmin topic
ubertmux --topic development    # Development topic
ubertmux --list-topics          # Show all topics
```

## Example Workflow

```bash
# System administration context
ubertmux --topic sysadmin
# → Starts in /var/log, ready for log monitoring

# Switch to development context  
ubertmux --topic development
# → Starts in ~/projects, separate environment

# Health data analysis
ubertmux --topic health
# → Dedicated space for health scripts and data
```

## Topic Organization

Each topic creates an isolated tmux session:

- **Session naming**: `ubertmux-<topic>` (e.g., `ubertmux-sysadmin`)
- **Workspace support**: `UBERTMUX_WORKSPACE_SYSADMIN="/var/log"`
- **Independent environments**: Separate history, processes, layouts
- **Native integration**: Use Ctrl+A + s to switch between all topics

## Documentation

**📖 [Complete Documentation](ubertmux.README.md)** - Detailed setup, configuration, and advanced usage

**🎮 [Quick Reference](ubertmux.README.md#key-bindings)** - All keybindings and commands

**🔧 [Configuration Guide](ubertmux.README.md#configuration)** - Customization options

**💡 [Use Cases](ubertmux.README.md#use-cases)** - Real-world workflow examples

**🛠 [Tips & Tricks](TIPS.md)** - OSC-title propagation, smart zoom, window-switch chords that don't fight inner tmux

**📝 [Extended example config](examples/ubertmux.conf.example)** - Drop-in `~/.ubertmux.conf` with everything from TIPS.md wired up

## Requirements

- tmux (any recent version)
- Bash shell environment

## Part of shibuido

UberTmux is part of the [shibuido](https://github.com/shibuido) collection - tools following "the way of subtle, unobtrusive beauty in utility."

---

**Ready to organize your terminal workflow?** → [Read the full documentation](ubertmux.README.md)