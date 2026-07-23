# IdleScreen

Wayland-native idle screen and ambient display for Linux. Modular visual
effects, optional COSMIC panel integration, and signed APT/DNF packages.
Built in Rust.

## Core

| Repository | Role |
|------------|------|
| [idlescreen](https://github.com/idlescreen/idlescreen) | Daemon, CLI, TUI, plugin API (package name still `trance` for now) |
| [idlescreen-applet](https://github.com/idlescreen/idlescreen-applet) | Optional COSMIC Desktop panel applet |
| [packages](https://github.com/idlescreen/packages) | APT and DNF package index |
| [brand](https://github.com/idlescreen/brand) | Brand assets |

## Official plugins

| Repository |
|------------|
| [plugin-beams](https://github.com/idlescreen/plugin-beams) |
| [plugin-bursts](https://github.com/idlescreen/plugin-bursts) |
| [plugin-chaos](https://github.com/idlescreen/plugin-chaos) |
| [plugin-cosmos](https://github.com/idlescreen/plugin-cosmos) |
| [plugin-glyphs](https://github.com/idlescreen/plugin-glyphs) |
| [plugin-gnats](https://github.com/idlescreen/plugin-gnats) |
| [plugin-hearth](https://github.com/idlescreen/plugin-hearth) |
| [plugin-radar](https://github.com/idlescreen/plugin-radar) |
| [plugin-ripple](https://github.com/idlescreen/plugin-ripple) |
| [plugin-storm](https://github.com/idlescreen/plugin-storm) |

## Install

Package index: [idlescreen.github.io/packages](https://idlescreen.github.io/packages/)

```bash
# Debian / Ubuntu / Pop!_OS — after adding the IdleScreen apt source
sudo apt install trance
# optional COSMIC applet
sudo apt install trance-applet
# optional effects
sudo apt install trance-plugin-beams
```

## Related

Self-hosted web apps: [studio2201](https://github.com/studio2201)
