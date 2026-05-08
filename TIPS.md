# UberTmux Tips & Tricks

Practical patterns and gotchas accumulated while running ubertmux as a daily driver. Pairs with [`examples/ubertmux.conf.example`](examples/ubertmux.conf.example).

## OSC-title propagation: nested tmux → ubertmux → terminal tab

By default, every ubertmux window tab shows the same string (the wrapper or shell command name), which makes a busy ubertmux session unreadable. Fix it by chaining OSC-2 titles through three stages.

### The chain

```
inner tmux ──OSC 2──► ubertmux pane PTY ──captured as #T──► ubertmux ──OSC 2──► terminal tab
                                                       └─► used as window-tab name
```

1. **Inner tmux emits the title.** In your inner `~/.tmux.conf`:

    ```tmux
    set -g set-titles on
    set -g set-titles-string "#S | #W"
    ```

    tmux writes `ESC ] 2 ; <session> | <window> BEL` to its PTY whenever the focused session/window changes.

2. **Ubertmux captures it.** This is automatic tmux behavior — no flag controls it. The latest OSC title becomes the pane's `pane_title` (`#T`).

3. **Ubertmux re-uses `#T` two ways.** In `~/.ubertmux.conf`:

    ```tmux
    set -g set-titles on
    set -g set-titles-string "#T"
    set -g automatic-rename-format '#{?#{==:#{pane_title},},#{pane_current_command},#{pane_title}}'
    ```

    * `set-titles-string "#T"` re-emits the inner title upward to your terminal emulator.
    * `automatic-rename-format` makes the ubertmux window tab show `#T` instead of the (often identical) `pane_current_command`.

### Konsole-specific gotcha

Even with the above wired up, **KDE Konsole's tab won't change** unless its tab-title format includes `%w`. The default profile is often `%d : %n` (cwd : program), which silently ignores OSC titles.

Diagnose:

```bash
printf '\e]2;HELLO_KONSOLE\a'
```

If the tab does *not* change to `HELLO_KONSOLE`, open Settings → Edit Current Profile → Tabs and add `%w` to "Tab title format" (and "Remote tab title format" if you SSH).

Useful Konsole tab-format codes:

* `%w` — OSC window title (what tmux sends)
* `%n` — program name (`bash`, `tmux`, `ssh`)
* `%d` / `%D` — cwd basename / full path
* `%u` / `%H` — user / remote host (remote format only)

### Caveats

* `automatic-rename` auto-disables for any window where the user (or a script) called `rename-window`. Re-enable in bulk:

    ```bash
    for w in $(tmux list-windows -t ubertmux -F '#{window_index}'); do
      tmux setw -t ubertmux:$w automatic-rename on
    done
    ```

* tmux only re-evaluates `automatic-rename-format` on a trigger (process change, OSC title change). After editing the format string, existing windows keep their old names — toggle `automatic-rename off → on` to force refresh.

* If the inner pane is plain `bash` (no tmux, no TUI setting a title), `pane_title` is empty. The `?:` fallback above handles this by showing `pane_current_command`.

## Window-switching shortcuts that don't fight inner tmux

Binding `Ctrl+Shift+{Left,Right}` at ubertmux's `-n` (root) level is tempting but **breaks inner tmux** on KDE+Konsole, where those chords are the most reliable way to switch inner windows. Use combinations the inner tmux doesn't claim:

* `Ctrl+h` / `Ctrl+l` — vim-style, no prefix.
* `Ctrl+Alt+y` / `Ctrl+Alt+o` — `y` sits above `h`, `o` sits above `l` on QWERTY (mnemonic: same h/l semantics, one row up). Confirmed working on KDE+Konsole and Termux on Android.

See `examples/ubertmux.conf.example` section 4 for the bindings.

## Smart zoom: `prefix+g`

A single key that does the right thing depending on state:

* If a pane is already zoomed → un-zoom.
* Otherwise → show numbered `display-panes` overlay for 10 minutes, then zoom whichever pane you pick.

```tmux
unbind-key g
bind-key g \
  if-shell -F '#{window_zoomed_flag}' 'resize-pane -Z' '' \; \
  display-panes -d 600000 "select-pane -t '%%'; resize-pane -Z"
```

Pairs well with multi-pane workflows where you frequently maximize one pane to read output, then collapse back.

## Workspace-bound new windows

Topics already give you per-context sessions; you can also force `new-window` to start in a chosen directory:

```tmux
bind c new-window -c "$HOME/w/ubertmux-w"
```

Useful when ubertmux is your "outer shell" and you want every fresh window rooted at a scratch / notes directory regardless of where the previous pane was.

## High-contrast / eInk status

For eInk laptops or high-glare environments:

```tmux
set -g status-style              fg=#ffffff,bg=#000000
set -g window-status-style       fg=#ffffff,bg=#000000
set -g window-status-current-style fg=#000000,bg=#ffffff
```

## Big paste buffer for image-in-terminal tools

`chafa`, Sixel renderers, and other image-to-text tools paste large blobs that overflow tmux's default 1 MB input buffer. Bump it:

```tmux
set -s input-buffer-size 16777216
```

Reference: [chafa#327](https://github.com/hpjansson/chafa/issues/327).

## True-color and extended keys

```tmux
set-option -ga terminal-overrides ",xterm-256color:Tc"
set -s extended-keys on
set -as terminal-features 'xterm*:extkeys'
```

`extended-keys` (tmux ≥ 3.2) lets the terminal disambiguate `Ctrl+Shift+letter` and similar chords, which most modifier-heavy bindings rely on.
