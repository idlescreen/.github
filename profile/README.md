<div align="center">

<img src="assets/idlescreen-org-icon.jpg" alt="IdleScreen" width="128" />

# IdleScreen

**High-performance Wayland-native idle screen and ambient screensaver suite for Linux.**

[Website](https://idlescreen.github.io/) • [Documentation](https://idlescreen.github.io/) • [Packages Channel](https://github.com/idlescreen/packages) • [Core Monorepo](https://github.com/idlescreen/idlescreen)

</div>

IdleScreen (`v4.2.0`) provides zero-waste ambient displays and screensavers for Linux desktops on Wayland. It features an event-driven daemon with 0.00% CPU at idle (~2–3 MB resident), 1x native grid GPU scaling via Wayland `wp_viewporter`, dynamic 60–144 Hz monitor refresh rate matching, first-class **SuperFedora** integration, and a clean OS-only (or custom text) 16-bit arcade banner across **37 in-tree ASCII effects** and **11 `idlescreen-extras` mathematical savers**.

---

## Repositories & Architecture

### Core Active Repositories
| Repository | Role |
|---|---|
| [**idlescreen**](https://github.com/idlescreen/idlescreen) | **Core Monorepo (`v4.2.0`)**: Unified high-performance screensaver suite containing the daemon, CLI, TUI, 37-effect ASCII engine, `idlescreen-extras` (11 mathematical savers), and `idlescreen-cosmic` panel applet. |
| [**packages**](https://github.com/idlescreen/packages) | **Package Channel & Automation**: Signed package repositories (APT, RPM, Arch Linux PKGBUILD), cryptographic verification, and the universal `install.sh` bootstrap. |
| [**idlescreen.github.io**](https://github.com/idlescreen/idlescreen.github.io) | **Product Website**: Web showcase, 48-scene interactive matrix, documentation, and download portal. |

### Specialized & Companion Repositories
| Repository | Role |
|---|---|
| [**studio**](https://github.com/idlescreen/studio) | **Offline Video Engine & Director**: Headless rendering pipeline to AV1/H.264 video (`idle-studio`). |

### Archived Legacy Repositories
> [!NOTE]
> The standalone legacy crates [**`cli`**](https://github.com/idlescreen/cli), [**`tui`**](https://github.com/idlescreen/tui), [**`runtime`**](https://github.com/idlescreen/runtime), [**`savers`**](https://github.com/idlescreen/savers), and [**`cosmic`**](https://github.com/idlescreen/cosmic) have been consolidated into the [**`idlescreen`**](https://github.com/idlescreen/idlescreen) monorepo and are archived on GitHub in read-only mode for historical reference.

---

## Quick Install

```sh
curl -fsSL https://idlescreen.github.io/install.sh | sh
```

Verify installer SHA-256 before running:
```sh
curl -fsSL https://idlescreen.github.io/install.sh -o install.sh
./install.sh --verify
./install.sh
```

---

## Core Commands

```sh
idlescreen status                  # check daemon, inhibitor, and screensaver status
idlescreen preview [saver]         # preview any of the 48 savers in terminal or fullscreen
idlescreen text [text | --clear]   # set custom banner text or reset to OS-only wordmark
idlescreen tui                     # launch interactive configuration dashboard
idlescreen list                    # list 37 built-in effects and 11 extras savers
idlescreen inhibitors              # inspect active D-Bus / portal / audio idle inhibitors
idlescreen service install         # install user service (systemd, openrc, runit, s6, dinit, xdg)
idlescreen doctor --fix            # diagnose and auto-remediate Wayland, socket, and service issues
idlescreen config                  # display active TOML configuration
```

---

## License

Apache-2.0 · © 2026 IdleScreen
