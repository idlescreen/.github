# IdleScreen

Modular Wayland screensavers for Linux — an idle daemon, a family of
procedural saver plugins, and the tools around them, packaged for
Fedora (RPM) and Debian (APT).

| Repo | Role |
|---|---|
| [idlescreen](https://github.com/idlescreen/idlescreen) | Front door: routes to every component, installs the product stack |
| [cli](https://github.com/idlescreen/cli) | Daemon protocol commands (`status`, `preview`, `doctor`, …) |
| [runtime](https://github.com/idlescreen/runtime) | Idle daemon, sandboxed plugin runtime, D-Bus API |
| [savers](https://github.com/idlescreen/savers) | 11 procedural savers (aurora, beams, bursts, chaos, cosmos, glyphs, gnats, hearth, radar, ripple, storm) + the `idle-savers` bundle |
| [studio](https://github.com/idlescreen/studio) | Offline saver → video engine + Director TUI (AV1/H.264) |
| [tui](https://github.com/idlescreen/tui) | Runtime configuration TUI |
| [cosmic](https://github.com/idlescreen/cosmic) | COSMIC panel applet |
| [packages](https://github.com/idlescreen/packages) | Signed APT + RPM package channel |
| [idlescreen.github.io](https://github.com/idlescreen/idlescreen.github.io) | Product site |

## Install

```sh
curl -fsSL https://idlescreen.github.io/packages/install.sh | sh
```

## Commands

```sh
idlescreen components   # component catalog + install state
idlescreen status       # daemon state, active saver, inhibitors
idlescreen preview storm
idlescreen tui          # runtime configuration
```

## License

Apache-2.0 · © 2026 IdleScreen
