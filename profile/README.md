# IdleScreen

**Wayland-native idle screen and ambient display for Linux.**  
Rust · Apache-2.0 · signed packages.

## Install a product (not the engine)

```bash
# COSMIC
sudo dnf install idle-cosmic
systemctl --user enable --now idle-daemon
idle status

# Live TUI (optional)
sudo dnf install idle-tui
```

| Product | Repo |
|---------|------|
| **idle-cosmic** | [idle-cosmic](https://github.com/idlescreen/idle-cosmic) |
| **idle-tui** | [idle-tui](https://github.com/idlescreen/idle-tui) |
| **idle-studio** | [idle-studio](https://github.com/idlescreen/idle-studio) |
| **idle-windows** | [idle-windows](https://github.com/idlescreen/idle-windows) |
| **idle-steam** | [idle-steam](https://github.com/idlescreen/idle-steam) |

Engine source: **[idle](https://github.com/idlescreen/idle)** → packages `idle-daemon`, `idle-cli` (command **`idle`**), `idle-savers`.  
**Do not install the engine package as a product.**

## Effects · ops

- **idle-saver-*** — official plugins  
- **[packages](https://github.com/idlescreen/packages)** — [APT/DNF index](https://idlescreen.github.io/packages/)  
- **[brand](https://github.com/idlescreen/brand)** · **[render](https://github.com/idlescreen/render)** · **[idle-pro](https://github.com/idlescreen/idle-pro)**

## Docs

[TARGET](https://github.com/idlescreen/.github/blob/main/docs/TARGET.md) ·
[REPOS](https://github.com/idlescreen/.github/blob/main/docs/REPOS.md) ·
[BOUNDARIES](https://github.com/idlescreen/.github/blob/main/docs/BOUNDARIES.md)
