# AGENTS.md — Echofloat

Guidance for AI coding agents working in this repository. Human contributors: see
[`CONTRIBUTING.md`](CONTRIBUTING.md); users: see [`README.md`](README.md).

**What this is:** Echofloat is a native macOS menu-bar app that shows animated,
always-visible lyrics and playback controls, tracking the active supported player
(YouTube Music in Chrome, Apple Music, Spotify). Swift + SwiftUI/AppKit, packaged
with Swift Package Manager.

## Commands you must run

From the repository root:

```bash
swift build                          # compile the app
bash Tests/PackagingChecks.sh        # app bundle / packaging invariants
bash Tests/InstallerChecks.sh        # installer script invariants
```

With Swift 6.1 or newer (required for the full Swift Testing suite):

```bash
swift test --no-parallel
bash Tests/ToolchainCompatibilityChecks.sh
```

The app builds with Swift 5.9+ via `Package.swift`; the full test suite uses the
version-specific `Package@swift-6.1.swift` manifest. **Never claim a change is done
until the relevant checks above pass** — show the output, don't assert.

## Non-negotiable conventions

- **No private Apple frameworks.** Use only public APIs.
- **No borrowed artwork or copied media.** Do not add placeholder, borrowed, or
  broken-link images. Repository-owned assets live under `Resources/`.
- **Player control is AppleScript/Apple Events + Automation.** Now-playing state and
  transport come through the `MediaSources` layer; macOS Automation approval is
  required at runtime. Don't hard-code app-specific hacks outside that layer.
- **Keep secrets and tokens out.** Echofloat uses no account, session token,
  analytics, or telemetry — keep it that way.
- **Update docs when user-facing behavior changes.** If you touch setup, install,
  uninstall, Launch-at-Login, privacy, or permissions, update `README.md` and
  `CONTRIBUTING.md` in the same change and verify the setup/removal paths.
- **Minimal, surgical diffs.** Keep changes focused and easy to review; preserve
  existing safety and permission guards. Add tests for behavior changes.

## Adding a theme

Define a new `Theme` in `Sources/echofloat/Theming/Theme.swift` and append it to
`Theme.builtIn`; it becomes available from the menu bar's Theme submenu.

## Layout

| path | job |
|------|-----|
| `Sources/echofloat/App` | app wiring, delegate, commands, entry point |
| `Sources/echofloat/MediaSources` | now-playing state + transport for each player |
| `Sources/echofloat/Lyrics` | LRCLIB fetch, parse, and local cache |
| `Sources/echofloat/MenuBar` | menu-bar status item and menu |
| `Sources/echofloat/Overlay` | the floating lyrics/controls panel and placement |
| `Sources/echofloat/Theming` | built-in themes and motion motifs |
| `Sources/echofloat/Support` | Launch at Login (`SMAppService`) and app lifecycle |
| `Resources/` | app icon, `Info.plist` (repository-owned assets only) |
| `Tests/` | Swift Testing suites + packaging/installer/toolchain shell checks |

## Workflow

- **Pull requests:** open a PR and let a human merge it. **Never self-merge.** Call
  out any privacy or permission change explicitly in the PR description.
- Include screenshots only for visual changes and only using repository-owned images.
