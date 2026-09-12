# IdleScreen

Modular Wayland screensavers for Linux — a native idle daemon plus a family of
procedural saver plugins, packaged for Fedora (RPM) and Debian (APT).

- **[idle](https://github.com/idlescreen/idle)** — daemon, plugin runtime, CLI, Landlock-sandboxed plugin loading
- **[idle-savers](https://github.com/idlescreen/idle-savers)** — 11 procedural savers (aurora, beams, bursts, chaos, cosmos, glyphs, gnats, hearth, radar, ripple, storm)
- **[render](https://github.com/idlescreen/render)** — offline saver → video encoder + idle-studio TUI (AV1/H.264)
- **[idle-tui](https://github.com/idlescreen/idle-tui)** · **[idle-cosmic](https://github.com/idlescreen/idle-cosmic)** — runtime TUI, COSMIC applet
- **[packages](https://github.com/idlescreen/packages)** — signed APT + RPM package channel (`curl | sh` installer)

Install:

```sh
curl -fsSL https://idlescreen.github.io/packages/install.sh | sh
```
