# IdleScreen

Wayland-native idle screen and ambient display for Linux. Modular effects,
optional COSMIC panel integration, offline video render tools, and signed
APT/DNF packages. Built in Rust under the Apache-2.0 license.

## Core

| Repository | Role |
|------------|------|
| [idle-core](https://github.com/idlescreen/idle-core) | Daemon, CLI, TUI, plugin API |
| [idle-cosmic](https://github.com/idlescreen/idle-cosmic) | COSMIC Desktop panel applet |
| [idle-packages](https://github.com/idlescreen/idle-packages) | Linux APT and DNF package index |
| [idle-brand](https://github.com/idlescreen/idle-brand) | Brand assets |

## Create (offline video)

| Repository | Role |
|------------|------|
| [idle-render](https://github.com/idlescreen/idle-render) | Offline sim → video (CLI) |
| [idle-studio](https://github.com/idlescreen/idle-studio) | Director TUI / queue / long-form |
| [idle-pro](https://github.com/idlescreen/idle-pro) | Monetization and product strategy |

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

Package index: [idlescreen.github.io/idle-packages](https://idlescreen.github.io/idle-packages/)

```bash
# After adding the IdleScreen apt source
sudo apt install trance
sudo apt install trance-applet          # COSMIC
sudo apt install trance-plugin-beams    # effect
```

Shipped package names remain `trance` / `trance-*` until a coordinated rename.
The product brand and repositories are IdleScreen / `idle-*` / `saver-*`.

## Map

Full target architecture: [docs/TARGET.md](https://github.com/idlescreen/.github/blob/main/docs/TARGET.md)

## Related

Self-hosted web and container projects: [studio2201](https://github.com/studio2201)
