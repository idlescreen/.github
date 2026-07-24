# IdleScreen target architecture

Human brand: **IdleScreen** · GitHub org: **idlescreen**

## Naming

| Pattern | Meaning |
|---------|---------|
| `idle-*` apps | Products people install (`idle-cosmic`, `idle-tui`, …) |
| `idle-core` | Daemon source; ship package **`idle`**, CLI command **`idle`** |
| `idle-saver-*` | Official effects |
| `render` | Offline export engine |
| `packages` / `brand` | Host + identity (short names kept) |

## Repos

```
idle-cosmic idle-tui idle-studio idle-windows idle-steam
idle-core idle-pro
idle-saver-{beams,bursts,chaos,cosmos,glyphs,gnats,hearth,radar,ripple,storm}
render packages brand .github
```

## Install

```bash
sudo dnf install idle-cosmic   # full COSMIC stack
idle status                    # CLI
```

## Former names

| Old | New |
|-----|-----|
| app-cosmic / idlescreen | idle-cosmic / **idle** |
| app-tui | idle-tui |
| app-studio | idle-studio |
| saver-* | idle-saver-* |
| idlescreen (package) | **idle** |

## Architecture law

[BOUNDARIES.md](BOUNDARIES.md)
