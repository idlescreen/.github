# IdleScreen

Modular Wayland screensavers for Linux — a native idle daemon plus a family of
procedural saver plugins, packaged for Fedora (RPM) and Debian (APT).

- **[idlescreen](https://github.com/idlescreen/idlescreen)** — the `idlescreen` front door: routes to every component, installs the product stack
- **[cli](https://github.com/idlescreen/cli)** — daemon protocol commands (`idlescreen status`, `preview`, `doctor`, …)
- **[runtime](https://github.com/idlescreen/runtime)** — daemon, plugin runtime, Landlock-sandboxed plugin loading, D-Bus API
- **[savers](https://github.com/idlescreen/savers)** — 11 procedural savers (aurora, beams, bursts, chaos, cosmos, glyphs, gnats, hearth, radar, ripple, storm) + the `idle-savers` bundle
- **[studio](https://github.com/idlescreen/studio)** — offline saver → video encoder + idle-studio TUI (AV1/H.264)
- **[tui](https://github.com/idlescreen/tui)** · **[cosmic](https://github.com/idlescreen/cosmic)** — runtime TUI, COSMIC applet
- **[packages](https://github.com/idlescreen/packages)** — signed APT + RPM package channel (`curl | sh` installer)

Install:

```sh
curl -fsSL https://idlescreen.github.io/packages/install.sh | sh
```

Then everything is one command away: `idlescreen components`.
