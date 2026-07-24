# IdleScreen

Wayland-native idle screen and ambient display for Linux. Shared engines,
platform apps, offline video tools, and signed APT/DNF packages.
Built in Rust under the Apache-2.0 license.

## Apps (platform products)

| Repository | Role |
|------------|------|
| [app-cosmic](https://github.com/idlescreen/app-cosmic) | COSMIC Desktop (applet + product) |
| [app-kde](https://github.com/idlescreen/app-kde) | KDE Plasma (stub) |
| [app-windows](https://github.com/idlescreen/app-windows) | Windows host (stub) |
| [app-steam](https://github.com/idlescreen/app-steam) | Steam / Deck (stub) |

## Engines

| Repository | Role |
|------------|------|
| [idle-core](https://github.com/idlescreen/idle-core) | Daemon, plugin API, CLI |
| [idle-tui](https://github.com/idlescreen/idle-tui) | Optional live TUI |
| [idle-render](https://github.com/idlescreen/idle-render) | Offline sim → video |
| [idle-studio](https://github.com/idlescreen/idle-studio) | Director TUI / long-form |
| [idle-pro](https://github.com/idlescreen/idle-pro) | Monetization strategy |
| [idle-brand](https://github.com/idlescreen/idle-brand) | Brand assets |
| [packages](https://github.com/idlescreen/packages) | Public APT/DNF host |

## Official effects (`saver-*`)

| Repository |
|------------|
| [saver-beams](https://github.com/idlescreen/saver-beams) |
| [saver-bursts](https://github.com/idlescreen/saver-bursts) |
| [saver-chaos](https://github.com/idlescreen/saver-chaos) |
| [saver-cosmos](https://github.com/idlescreen/saver-cosmos) |
| [saver-glyphs](https://github.com/idlescreen/saver-glyphs) |
| [saver-gnats](https://github.com/idlescreen/saver-gnats) |
| [saver-hearth](https://github.com/idlescreen/saver-hearth) |
| [saver-radar](https://github.com/idlescreen/saver-radar) |
| [saver-ripple](https://github.com/idlescreen/saver-ripple) |
| [saver-storm](https://github.com/idlescreen/saver-storm) |

## Install

Package index: [idlescreen.github.io/packages](https://idlescreen.github.io/packages/)

```bash
sudo apt install idlescreen
sudo apt install idlescreen-applet   # COSMIC app
sudo apt install idlescreen-tui      # optional TUI
sudo apt install saver-beams
sudo apt install idlescreen-cosmic   # product meta (daemon+applet+savers)
```

Legacy `trance*` package names are Obsoleted/Provided by the IdleScreen names above.

## Map

[docs/TARGET.md](https://github.com/idlescreen/.github/blob/main/docs/TARGET.md)

## Architecture

First-principle boundaries (kernel, compositor, DE vs IdleScreen):
[docs/BOUNDARIES.md](https://github.com/idlescreen/.github/blob/main/docs/BOUNDARIES.md)
