# IdleScreen target architecture

Human brand: **IdleScreen**  
GitHub org: **idlescreen**

## Naming

| Pattern | Meaning |
|---------|---------|
| `app-*` | Platform products (DE / OS / store) |
| `idle-*` | Shared engines, tools, brand, business |
| `packages` | Public APT/DNF host (short name for URL) |
| `saver-*` | Official visual effects |

## Repository inventory (ABC)

```
.github
app-cosmic
app-kde
app-steam
app-windows
idle-brand
idle-core
idle-pro
idle-render
idle-studio
idle-tui
packages
saver-beams
saver-bursts
saver-chaos
saver-cosmos
saver-glyphs
saver-gnats
saver-hearth
saver-radar
saver-ripple
saver-storm
```

## Responsibilities

| Repo | Responsibility |
|------|----------------|
| app-cosmic | COSMIC applet + product metapackage recipe |
| app-kde | Plasma app (stub) |
| app-windows | Windows host (stub) |
| app-steam | Steam / Deck packaging (stub) |
| idle-core | Daemon, plugin API, **CLI** |
| idle-tui | Optional live TUI (`trance-tui` binary) |
| idle-render | Offline fixed-dt simulation → video |
| idle-studio | Director TUI; queue, long-form, music |
| idle-pro | Monetization and product strategy |
| idle-brand | Icons and visual identity |
| packages | APT/DNF signing + Pages index |
| saver-* | One effect per repo |

## Data flow

```
saver-*  -->  idle-core     (live presentation + CLI)
         \->  idle-render   (offline frames → AV1)
                   ^
                   |
             idle-studio

idle-tui -----> idle-core D-Bus/IPC (optional controller)

app-* --------> depend on idle-core + saver-* (+ platform UI)
```

## Install (product)

`app-cosmic` metapackage → core (+ CLI) + savers + applet; Recommends idle-tui.

## Explicitly out

| Name | Why |
|------|-----|
| app-gnome | Parked (poor platform fit) |
| idle-cli repo | CLI stays in idle-core |
| idle-packages | Use plain `packages` |

## Former names (redirects)

| Old | New |
|-----|-----|
| idlescreen | idle-core |
| idlescreen-applet / idle-cosmic | app-cosmic |
| brand | idle-brand |
| plugin-* | saver-* |

## Architecture law

First-principle OS/compositor/DE boundaries are locked in
[BOUNDARIES.md](BOUNDARIES.md). Read that before expanding idle-core or apps
into new layers of the stack.
