# IdleScreen target architecture

Human brand: **IdleScreen**  
GitHub org: **idlescreen**

## Naming

| Pattern | Meaning |
|---------|---------|
| `idle-*` | Product, tools, brand |
| `packages` | Linux APT/DNF index (short name for host URL) |
| `saver-*` | Official visual effects |

## Repository inventory (ABC)

```
.github
idle-brand
idle-core
idle-cosmic
packages
idle-pro
idle-render
idle-studio
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
| idle-core | Live Wayland daemon, plugin API, live CLI/TUI |
| idle-cosmic | COSMIC applet only |
| packages | APT/DNF signing + Pages index (Linux) |
| idle-brand | Icons and visual identity |
| idle-render | Offline fixed-dt simulation → video encode |
| idle-studio | Director TUI; jobs, segments, music, presets |
| idle-pro | Monetization, SKUs, channel strategy docs |
| saver-* | One effect per repo; loads via plugin API |

## Data flow

```
saver-*  -->  idle-core     (live presentation)
         \->  idle-render   (offline frames → AV1)
                   ^
                   |
             idle-studio    (queue / creative control)
```

## Out of scope for packages

- Winget: manifests in microsoft/winget-pkgs (optional helper later)
- Steam: separate Steamworks pipeline when needed
- Flatpak: Flathub or idle-core packaging/ when needed

## Historical / stable for now

- Debian/crate binary names: `trance*`
- D-Bus well-known names (ABI)
- Keyring filenames on the package host may still say `crateria-*`

## Former names (GitHub redirects)

| Old | New |
|-----|-----|
| idlescreen | idle-core |
| idlescreen-applet | idle-cosmic |
| packages | packages |
| brand | idle-brand |
| plugin-* | saver-* |
