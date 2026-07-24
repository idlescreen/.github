# IdleScreen

**Wayland-native idle screen and ambient display for Linux.**  
Rust · Apache-2.0 · signed APT & DNF packages.

> Host pure visual plugins, present them when the session is idle, and control them over D-Bus — without becoming a compositor or lock screen.

| | |
|---|---|
| **Install** | [idlescreen.github.io/packages](https://idlescreen.github.io/packages/) |
| **Core** | [idle-core](https://github.com/idlescreen/idle-core) |
| **Architecture** | [BOUNDARIES.md](https://github.com/idlescreen/.github/blob/main/docs/BOUNDARIES.md) |
| **Repo map** | [TARGET.md](https://github.com/idlescreen/.github/blob/main/docs/TARGET.md) |

---

## Quick install (Fedora COSMIC)

```bash
sudo curl -fsSL https://idlescreen.github.io/packages/rpm/crateria.repo \
  -o /etc/yum.repos.d/idlescreen.repo
sudo dnf install app-cosmic
systemctl --user enable --now idlescreen-daemon
```

`app-cosmic` pulls the **daemon**, **all official savers**, and the **COSMIC panel applet**.

Debian / Pop!_OS: same product name via APT — see the [packages index](https://idlescreen.github.io/packages/).

---

## How the org is laid out

```text
app-*        Platform products (desktop / OS / store / controllers)
idle-core    Daemon, plugin API, CLI
render       Offline export (sim → video)
saver-*      One official visual effect each
packages     Public APT/DNF host
brand        Icons and identity
idle-pro     Product / Pro strategy
```

---

## Start here

| Repo | What it is |
|------|------------|
| **[idle-core](https://github.com/idlescreen/idle-core)** | Daemon, D-Bus API, CLI, plugin host |
| **[app-cosmic](https://github.com/idlescreen/app-cosmic)** | COSMIC product package |
| **[packages](https://github.com/idlescreen/packages)** | Signed `.deb` / `.rpm` index |
| **[brand](https://github.com/idlescreen/brand)** | Icons, headers, avatar |

---

## Platform apps (`app-*`)

| Repository | Role |
|------------|------|
| [app-cosmic](https://github.com/idlescreen/app-cosmic) | **COSMIC** — product package (applet + deps) |
| [app-tui](https://github.com/idlescreen/app-tui) | Live terminal controller |
| [app-studio](https://github.com/idlescreen/app-studio) | Director — queue offline renders |
| [app-kde](https://github.com/idlescreen/app-kde) | KDE Plasma *(stub)* |
| [app-windows](https://github.com/idlescreen/app-windows) | Windows *(stub)* |
| [app-steam](https://github.com/idlescreen/app-steam) | Steam / Deck *(stub)* |

---

## Engines & tooling

| Repository | Role |
|------------|------|
| [idle-core](https://github.com/idlescreen/idle-core) | Live presentation host + CLI |
| [render](https://github.com/idlescreen/render) | Offline sim → AV1 |
| [packages](https://github.com/idlescreen/packages) | APT + DNF publishing |
| [brand](https://github.com/idlescreen/brand) | Brand kit |
| [idle-pro](https://github.com/idlescreen/idle-pro) | Pro / monetization notes |

---

## Official effects (`saver-*`)

| | | |
|--|--|--|
| [beams](https://github.com/idlescreen/saver-beams) | [bursts](https://github.com/idlescreen/saver-bursts) | [chaos](https://github.com/idlescreen/saver-chaos) |
| [cosmos](https://github.com/idlescreen/saver-cosmos) | [glyphs](https://github.com/idlescreen/saver-glyphs) | [gnats](https://github.com/idlescreen/saver-gnats) |
| [hearth](https://github.com/idlescreen/saver-hearth) | [radar](https://github.com/idlescreen/saver-radar) | [ripple](https://github.com/idlescreen/saver-ripple) |
| [storm](https://github.com/idlescreen/saver-storm) | | |

---

## Naming cheat sheet

| You want… | Install / open… |
|-----------|------------------|
| Full COSMIC product | `dnf/apt install app-cosmic` |
| Daemon only | package `idlescreen` (repo **idle-core**) |
| Live TUI | `app-tui` |
| Offline video | `render` (+ optional **app-studio**) |
| Package feed | **packages** → [idlescreen.github.io/packages](https://idlescreen.github.io/packages/) |

Ship packages use IdleScreen names (`idlescreen*`, `app-*`, `saver-*`).  
Historical `trance*` names are provided/obsoleted for upgrades. D-Bus ABI stays stable.

---

## Docs

- [TARGET.md](https://github.com/idlescreen/.github/blob/main/docs/TARGET.md) — full repo map  
- [BOUNDARIES.md](https://github.com/idlescreen/.github/blob/main/docs/BOUNDARIES.md) — what IdleScreen owns vs compositor/DE  
- [REPOS.md](https://github.com/idlescreen/.github/blob/main/docs/REPOS.md) — catalog of every public repository  

## License

Product code is **Apache-2.0** unless a repo states otherwise.
