# IdleScreen

**Wayland-native idle screen and ambient display for Linux.**  
Rust · Apache-2.0 · signed APT & DNF packages.

| | |
|---|---|
| **Packages** | [idlescreen.github.io/packages](https://idlescreen.github.io/packages/) |
| **Core** | [idle-core](https://github.com/idlescreen/idle-core) → package **`idle`** |
| **Architecture** | [BOUNDARIES.md](https://github.com/idlescreen/.github/blob/main/docs/BOUNDARIES.md) |

---

## Install (COSMIC)

```bash
sudo curl -fsSL https://idlescreen.github.io/packages/rpm/crateria.repo \
  -o /etc/yum.repos.d/idlescreen.repo
sudo dnf install idle-cosmic
systemctl --user enable --now idle-daemon
idle status
```

`idle-cosmic` → daemon **`idle`** + all **`idle-saver-*`** + COSMIC applet.  
CLI command is **`idle`**.

---

## Layout

```text
idle-cosmic / idle-tui / idle-studio / idle-windows / idle-steam
idle-core          → package idle (+ idle-cli, idle-savers)
idle-saver-*       → effects
render             → offline export
packages · brand   → index + identity
```

---

## Products you install

| Package / repo | Role |
|----------------|------|
| **[idle-cosmic](https://github.com/idlescreen/idle-cosmic)** | COSMIC product (`dnf install idle-cosmic`) |
| **[idle-tui](https://github.com/idlescreen/idle-tui)** | Live TUI |
| **[idle-studio](https://github.com/idlescreen/idle-studio)** | Offline director |
| **[idle-windows](https://github.com/idlescreen/idle-windows)** | Windows *(stub)* |
| **[idle-steam](https://github.com/idlescreen/idle-steam)** | Steam *(stub)* |

## Engine

| Repo | Package / command |
|------|-------------------|
| [idle-core](https://github.com/idlescreen/idle-core) | **`idle`**, **`idle-cli`** → `idle` |
| [render](https://github.com/idlescreen/render) | offline video |
| [packages](https://github.com/idlescreen/packages) | APT/DNF host |
| [brand](https://github.com/idlescreen/brand) | brand kit |
| [idle-pro](https://github.com/idlescreen/idle-pro) | Pro notes |

## Effects

[idle-saver-beams](https://github.com/idlescreen/idle-saver-beams) ·
[bursts](https://github.com/idlescreen/idle-saver-bursts) ·
[chaos](https://github.com/idlescreen/idle-saver-chaos) ·
[cosmos](https://github.com/idlescreen/idle-saver-cosmos) ·
[glyphs](https://github.com/idlescreen/idle-saver-glyphs) ·
[gnats](https://github.com/idlescreen/idle-saver-gnats) ·
[hearth](https://github.com/idlescreen/idle-saver-hearth) ·
[radar](https://github.com/idlescreen/idle-saver-radar) ·
[ripple](https://github.com/idlescreen/idle-saver-ripple) ·
[storm](https://github.com/idlescreen/idle-saver-storm)

---

## Docs

- [TARGET.md](https://github.com/idlescreen/.github/blob/main/docs/TARGET.md)  
- [REPOS.md](https://github.com/idlescreen/.github/blob/main/docs/REPOS.md)  
- [BOUNDARIES.md](https://github.com/idlescreen/.github/blob/main/docs/BOUNDARIES.md)  

Apache-2.0 unless a repo says otherwise.
