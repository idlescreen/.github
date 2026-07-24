# IdleScreen target architecture

Human brand: **IdleScreen**  
GitHub org: **idlescreen**

## Naming

| Pattern | Meaning |
|---------|---------|
| `app-*` | Platform products (DE / OS / store / controller apps) |
| `idle-core` / `idle-pro` | Shared engine and business track |
| `brand` | Visual identity assets |
| `render` | Offline export engine |
| `packages` | Public APT/DNF host (short name for URL) |
| `saver-*` | Official visual effects |

## Repository inventory (ABC)

```
.github
app-cosmic
app-kde
app-steam
app-studio
app-tui
app-windows
brand
idle-core
idle-pro
packages
render
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
| app-cosmic | COSMIC product package (applet + requires daemon + all savers) |
| app-kde | Plasma app (stub) |
| app-windows | Windows host (stub) |
| app-steam | Steam / Deck packaging (stub) |
| app-tui | Optional live TUI controller |
| app-studio | Director; queue, long-form, music |
| idle-core | Daemon, plugin API, **CLI** |
| render | Offline fixed-dt simulation → video |
| idle-pro | Monetization and product strategy |
| brand | Icons and visual identity |
| packages | APT/DNF signing + Pages index |
| saver-* | One effect per repo |

## Data flow

```
saver-*  -->  idle-core     (live presentation + CLI)
         \->  render        (offline frames → AV1)
                   ^
                   |
             app-studio

app-tui -----> idle-core D-Bus/IPC (optional controller)

app-cosmic / app-*  --> depend on idle-core + saver-* (+ platform UI)
```

## Install (product)

```bash
sudo dnf install app-cosmic   # COSMIC: daemon + all savers + applet
```

Optional: `app-tui`, `idlescreen-cli`. Offline tools: `render`, `app-studio`.

## Explicitly out

| Name | Why |
|------|-----|
| app-gnome | Parked (poor platform fit) |
| idle-cli repo | CLI stays in idle-core |
| idle-packages | Use plain `packages` |

## Former names (redirects)

| Old | New |
|-----|-----|
| idlescreen (monorepo) | idle-core |
| idlescreen-applet / idle-cosmic | app-cosmic |
| idle-tui | app-tui |
| idle-studio | app-studio |
| idle-render | render |
| idle-brand / brand kit | brand |
| plugin-* | saver-* |

## Architecture law

First-principle OS/compositor/DE boundaries are locked in
[BOUNDARIES.md](BOUNDARIES.md). Read that before expanding idle-core or apps
into new layers of the stack.
