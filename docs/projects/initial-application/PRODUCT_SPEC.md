# Browpick — Product Specification

> A desktop-integrated browser profile picker that intercepts system URL-open requests and lets the user choose which browser and profile to open them in.

---

## 1. Problem Statement

On Arch Linux with Niri and the Noctalia (Quickshell) shell, clicking a link from *any* source — a terminal, a notification, a shell service (vault, AWS CLI, etc.) — goes straight to the system's default browser profile. There is no intermediate prompt. The user wants to:

- Keep multiple browser profiles (Firefox, Chrome, Chromium, Brave, etc.) installed and active
- Choose *per-request* which browser + profile opens a given URL
- Have this choice feel native to their desktop environment, not a terminal-only workflow

---

## 2. Vision

**Browpick** sits between the desktop's URL-handling layer and the actual browsers. When the system asks "open this URL," Browpick intercepts, shows a quick picker (dmenu/rofi/fzf/Quickshell-native), and launches the chosen browser with the correct profile flags.

### Guiding Principles

- **Zero-config to start**: Auto-detects browsers and profiles out of the box
- **Desktop-native UI**: Picker matches the user's existing workflow (dmenu-style, or Quickshell-native for Noctalia integration)
- **Lightweight**: No daemon required; runs per-request
- **Extensible**: Supports any browser that can be launched with a custom profile flag

---

## 3. Feature Set

### 3.1 Core: URL Interception

| Feature | Description | Priority |
|---------|-------------|----------|
| **xdg-open wrapper** | Replace/override `xdg-open` with a Browpick wrapper in `~/.local/bin/`. When any app calls `xdg-open <url>`, Browpick intercepts it and prompts the user. | P0 |
| **Desktop file handler** | Register `.desktop` files for `x-scheme-handler/http` and `x-scheme-handler/https` via `xdg-mime default`, so the interception works even for GUI apps that don't call `xdg-open` directly. | P0 |
| **XDG Desktop Portal support** | Optionally hook into `org.freedesktop.portal.OpenURI` for sandboxed apps (Flatpak, Snap) that route URL opens through the portal layer. | P1 |
| **Per-app routing rules** | Allow the user to define rules: "always open links from `aws-vault` in Firefox:Work", "links from `code` in Chrome:Personal". Skips the picker for matched apps. | P1 |
| **CLI passthrough** | If invoked without a URL (or with `--no-prompt`), fall back to the system default. Allows use as a normal `xdg-open` drop-in. | P2 |

### 3.2 Browser & Profile Detection

| Feature | Description | Priority |
|---------|-------------|----------|
| **Firefox profile scanning** | Parse `profiles.ini` from all known Firefox locations (`~/.mozilla/firefox/`, Flatpak path, Snap path). Extract profile names, paths, and default status. | P0 |
| **Chromium-family scanning** | Scan `~/.config/` for Chrome, Chromium, Brave, Edge, Vivaldi, Opera, and their beta/dev/canary variants. Detect `User Data` directories and sub-profiles (`Default`, `Profile 1`, etc.). | P0 |
| **Edge cases** | Handle `XDG_CONFIG_HOME` overrides, custom `--user-data-dir` paths, and browsers installed via AUR/Nix. | P0 |
| **Profile naming** | Display profiles as `Browser:ProfileName` (e.g., `Firefox:Personal`, `Chrome:Work`, `Brave:Private`). Allow custom display names via config. | P0 |
| **Profile preview** | Show profile metadata: last-used date, cookie count (approximate), bookmarks count. Helps distinguish similar profiles. | P1 |
| **Auto-detect running instances** | If a browser profile is already running, offer to reuse it (via D-Bus or `--no-new-instance`) instead of launching another. | P1 |

### 3.3 Picker UI

| Feature | Description | Priority |
|---------|-------------|----------|
| **dmenu/rofi/fzf backend** | Use a configurable dmenu-style binary as the picker. Default: `fzf` (if installed), fallback to `rofi -dmenu`, then `dmenu`. | P0 |
| **Quickshell-native picker** | Custom Quickshell (QML) picker widget designed for Niri/Noctalia integration. Shows browser icons, profile names, and a search bar. | P0 |
| **Keyboard navigation** | Arrow keys, Enter to confirm, Esc to cancel/fallback. Compatible with both dmenu-style and Quickshell picker. | P0 |
| **Fuzzy search** | Search across `Browser:Profile` entries. Supports partial matches (`ff w` → `Firefox:Work`). | P0 |
| **Recent history** | Show recently-used browser+profile at the top of the list. Configurable history size (default: 10). | P1 |
| **Timeout & auto-select** | Configurable timeout (default: 10s). If no selection, auto-select the last-used or default profile. | P1 |
| **Custom theme** | Quickshell picker supports theming via CSS/QML — match Noctalia's lavender aesthetic. | P2 |

### 3.4 Browser Launch

| Feature | Description | Priority |
|---------|-------------|----------|
| **Firefox: `-P <profile>`** | Launch Firefox with the selected profile using the `-P` flag. | P0 |
| **Chromium-family: `--user-data-dir`** | Launch Chrome/Chromium/Brave/etc. with `--user-data-dir=<path>` for the selected profile. | P0 |
| **Profile-specific flags** | Allow per-profile custom flags (e.g., `--kiosk`, `--disable-extensions`) via config. | P2 |
| **URL passthrough** | Forward the original URL to the browser as the last argument. | P0 |
| **Launch in background** | Use `nohup` or `setsid` to avoid blocking the calling process. | P0 |

### 3.5 Configuration

| Feature | Description | Priority |
|---------|-------------|----------|
| **Config file** | `~/.config/browpick/config.toml` (or `.yaml`). All settings in one place. | P0 |
| **Default browser** | Set a per-scheme default (`http`, `https`, `ftp`, etc.) to skip the picker for common cases. | P1 |
| **Excluded apps** | List of app names whose URL opens should bypass Browpick entirely (e.g., `firefox`, `google-chrome`). | P0 |
| **Picker binary** | Configurable picker backend: `fzf`, `rofi`, `dmenu`, `quickshell`, or `none` (fallback). | P0 |
| **Custom browser entries** | Manually add browsers not auto-detected (e.g., custom Nix installs, portable builds). | P1 |
| **Profile rename** | Override auto-detected profile display names in config. | P1 |
| **Verbose / debug mode** | Log what Browpick is doing for troubleshooting. | P2 |

### 3.6 Integration & Desktop Features

| Feature | Description | Priority |
|---------|-------------|----------|
| **Noctalia / Quickshell integration** | Native picker widget that blends with Noctalia's aesthetic. Optionally trigger via a Quickshell action or bar button. | P0 |
| **Niri tiling awareness** | Picker appears as a floating, transient window that doesn't disrupt the tiling layout. | P0 |
| **Browser icon display** | Show the browser's icon next to each entry in the picker. | P1 |
| **Notification on launch** | Optional popup notification showing which browser+profile was launched for which URL (first 80 chars). | P2 |
| **D-Bus API** | Expose a D-Bus interface so other apps can trigger Browpick programmatically. | P2 |

---

## 4. Architecture

```
┌─────────────────────────────────────────────────────┐
│                    Calling Application              │
│              (terminal, vault, aws, etc.)           │
└──────────────────────┬──────────────────────────────┘
                       │ calls xdg-open / portal
                       ▼
┌─────────────────────────────────────────────────────┐
│              Browpick Interceptor                   │
│                                                     │
│  1. Check per-app routing rules                     │
│  2. If no rule match → show picker                  │
│  3. If rule match → launch directly                 │
│  4. If no selection / timeout → use default         │
└──────────────────────┬──────────────────────────────┘
                       │ resolved browser + profile
                       ▼
┌─────────────────────────────────────────────────────┐
│              Browser Launcher                         │
│                                                     │
│  Firefox:  firefox -P "ProfileName" <url>           │
│  Chromium: chromium --user-data-dir=<path> <url>    │
│  Brave:    brave-browser --user-data-dir=<path> <url>│
│  ...etc                                          │
└─────────────────────────────────────────────────────┘
```

### Key Design Decisions

- **No daemon**: Browpick is a per-request process. This avoids state management complexity and is more secure (no persistent background process).
- **Config-first**: Auto-detection works out of the box, but config overrides everything.
- **Picker-agnostic core**: The detection and launch logic is independent of the picker UI. Swapping `fzf` for `rofi` or `quickshell` doesn't require changes to the core.

---

## 5. Tech Stack

| Component | Technology | Rationale |
|-----------|------------|-----------|
| **Language** | **Go** | Fast iteration, simple `os/exec` for browser launching, trivial fzf/dmenu integration, single static binary, clean AUR packaging |
| **Config format** | TOML (`github.com/BurntSushi/toml`) | Human-readable, well-supported in Go |
| **Picker (default)** | `fzf` | Fast fuzzy search, Arch package, widely used |
| **Picker (desktop)** | Quickshell (QML) / GTK dialog | Native to the user's desktop; GTK as fallback for portability |
| **Browser detection** | Filesystem scanning + `.desktop` file parsing | No dependencies on browser-specific tools |
| **URL scheme handler** | `xdg-mime` + `.desktop` file | Standard freedesktop mechanism |
| **Distribution** | AUR package + `go install` | Arch-first, but accessible to all |

---

## 6. User Journeys

### 6.1 First Run — Zero Config

```
$ xdg-open https://example.com
# Browpick interceptor triggers:

  ⚡ Browpick
  ┌─────────────────────────────────┐
  │ 🔍 Search browsers...           │
  ├─────────────────────────────────┤
  │ 🟠 Firefox:Personal             │
  │ 🔵 Chrome:Work                 │
  │ 🟢 Chromium:Default            │
  │ 🔴 Brave:Private               │
  ├─────────────────────────────────┤
  │ ⏱ Auto-selecting in 10s...    │
  └─────────────────────────────────┘

User selects "🔵 Chrome:Work" (or waits for timeout)
→ chromium --user-data-dir=~/.config/google-chrome/Work https://example.com
```

### 6.2 Configured — Per-App Routing

```
# In config.toml:
[routing]
aws-vault = "Firefox:Personal"
code = "Chrome:Work"

$ aws-vault login
# Browpick sees parent process is "aws-vault"
# Matches rule → Firefox:Personal opens directly (no picker)
```

### 6.3 Quickshell Picker (Noctalia)

A Quickshell-native picker that appears as a floating overlay matching Noctalia's lavender theme, with browser icons, fuzzy search, and recent history — all without requiring an external dmenu.

---

## 7. File Structure (Tentative)

```
browpick/
├── go.mod
├── main.go                # CLI entry point
├── cmd/
│   └── browpick/
│       └── main.go         # Binary entry point (go install target)
├── internal/
│   ├── config/
│   │   ├── config.go       # Config parsing + defaults (TOML)
│   │   └── model.go        # Config data structures
│   ├── scanner/
│   │   ├── scanner.go      # Browser scanner interface + orchestration
│   │   ├── firefox.go      # Firefox profile detection (profiles.ini)
│   │   ├── chromium.go     # Chromium-family detection (User Data dirs)
│   │   └── desktop_files.go # .desktop file parsing for browser discovery
│   ├── picker/
│   │   ├── picker.go       # Picker interface + orchestration
│   │   ├── fzf.go          # fzf backend
│   │   ├── rofi.go         # rofi backend
│   │   └── quickshell.go   # Quickshell/QML backend
│   ├── launcher/
│   │   ├── launcher.go     # Launch interface + orchestration
│   │   ├── firefox.go      # Firefox launcher (-P flag)
│   │   └── chromium.go     # Chromium launcher (--user-data-dir flag)
│   ├── interceptor/
│   │   ├── interceptor.go  # xdg-open wrapper / MIME handler logic
│   │   └── routing.go      # Per-app routing rules
│   └── model/
│       └── model.go        # Shared types (Browser, Profile, etc.)
├── data/
│   └── browpick.desktop    # MIME type registration
├── docs/
│   └── PRODUCT_SPEC.md
├── .gitignore
└── README.md
```

---

## 8. Non-Goals (Out of Scope for v1)

- **Syncing profiles across browsers** (Firefox Multi-Account Containers, Chrome's built-in sync, etc.)
- **Profile management** (creating, deleting, renaming profiles — Browpick only *reads* them)
- **Cross-platform support** (Linux-first; macOS/Windows are future considerations)
- **GUI config editor** (config is TOML; a GUI editor can come later)
- **Browser extension** (Browpick works at the system level, no extension needed)

---

## 9. Open Questions

1. **Quickshell picker implementation**: Can Quickshell render a floating, transient picker window that works well with Niri's tiling? Or should we use a GTK dialog (`zenity`/`yad`) as a bridge?
2. **Portal interception depth**: How much of the XDG Desktop Portal API can we hook into without becoming a portal backend ourselves? Might need `xdg-desktop-portal` introspection.
3. **Profile detection for Chromium**: Chromium doesn't expose a simple `profiles.ini` equivalent. Do we parse `Local State` JSON files, or scan directory names? Edge cases with named profiles vs. the `Default` folder.
4. **Flatpak/Snap browser detection**: These install browsers in isolated locations. Should we scan `~/.var/app/` for Flatpak browsers?
5. **Security model**: Since Browpick sits between the system and browsers, does it need any special sandboxing? Probably not — it's a user-space tool.
6. **Fallback chain**: What happens if the user cancels the picker? Options: use default browser, use last-used, or abort (error).

---

## 10. Phased Rollout

### Phase 1: CLI Core (MVP)
- Browser + profile detection (Firefox + Chromium-family)
- fzf/rofi/dmenu picker backend
- xdg-open wrapper
- Config file with basic settings
- Per-app routing rules

### Phase 2: Desktop Integration
- Quickshell-native picker for Noctalia
- Browser icon display
- Recent history in picker
- Timeout + auto-select
- Per-scheme defaults

### Phase 3: Polish & Extensibility
- XDG Desktop Portal support
- D-Bus API
- Custom browser entries
- Profile preview (metadata)
- Auto-detect running instances
- AUR package + documentation

---

*Last updated: 2026-05-05*
