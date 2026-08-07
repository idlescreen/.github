# IdleScreen

**IdleScreen** is a modular ambient screensaver host and idle-management suite for **Wayland** (strongest on COSMIC, Hyprland, Sway; GNOME/KDE vary by protocol support).

Website: [https://idlescreen.github.io](https://idlescreen.github.io)  
Org: [github.com/idlescreen](https://github.com/idlescreen)

---

## Quick install

**One-line installer:** Fedora / RHEL-family (**DNF**) and Debian / Ubuntu-family (**APT**).  
**Arch:** experimental `PKGBUILD` under [`packages/arch`](https://github.com/idlescreen/packages/tree/master/arch) — **not** covered by `install.sh`.

```bash
curl -fsSL https://idlescreen.github.io/packages/install.sh | sh
```

Installs the product metapackage **`idlescreen`** (`idle-daemon`, `idle-cli`, `idle-savers`, `idle-tui`), and **`idle-cosmic`** when COSMIC is detected.  
Remove: `sudo dnf remove idlescreen` / `sudo apt remove idlescreen`.

**Channel note:** The public package index is whatever is published on GitHub Pages. Local development trees may contain newer NVRs (e.g. 3.0.0) that are not public until `packages` is pushed.

---

## Official screensaver gallery

Ten procedural **cell-grid** plugins (host rasterizes; optional wgpu cell path, CPU fallback):

| Module | Preview |
|--------|---------|
| Beams, Cosmos, Bursts, Storm, Chaos | `idlescreen preview <name>` |
| Hearth, Ripple, Radar, Glyphs, Gnats | `idlescreen preview <name>` |

---

## Features (verified)

- **Wayland presentation** — needs idle-notify + layer-shell (or equivalent). See [BOUNDARIES](https://github.com/idlescreen/idle/blob/master/docs/BOUNDARIES.md).
- **CLI** — binary name **`idlescreen`** (does **not** install `/usr/bin/idle` — that is Fedora’s Python IDE).
- **TUI** — `idlescreen tui` / package `idle-tui`.
- **COSMIC applet** — optional package `idle-cosmic`.
- **Inhibit / battery** — logind + MPRIS2; on battery, present/sim caps at 30 FPS/Hz.

---

## Manual package install

<details>
<summary><b>Fedora / RHEL (DNF)</b></summary>

```bash
sudo curl -fsSL https://idlescreen.github.io/packages/rpm/idlescreen.repo \
  -o /etc/yum.repos.d/idlescreen.repo
sudo dnf check-update
sudo dnf install idlescreen
# COSMIC: sudo dnf install idle-cosmic
```
</details>

<details>
<summary><b>Debian / Ubuntu (APT)</b></summary>

```bash
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://idlescreen.github.io/packages/apt/idlescreen-keyring.gpg \
  | sudo tee /etc/apt/keyrings/idlescreen-keyring.gpg >/dev/null
echo "deb [signed-by=/etc/apt/keyrings/idlescreen-keyring.gpg] https://idlescreen.github.io/packages/apt stable main" \
  | sudo tee /etc/apt/sources.list.d/idlescreen.list >/dev/null
sudo apt update
sudo apt install idlescreen
# COSMIC: sudo apt install idle-cosmic
```
</details>

<details>
<summary><b>Arch (experimental PKGBUILD)</b></summary>

```bash
git clone https://github.com/idlescreen/packages.git
cd packages/arch
makepkg -si
```
</details>

---

## CLI (common)

```bash
idlescreen status
idlescreen enable | disable
idlescreen timeout <1-240>
idlescreen preview <saver>
idlescreen tui
idlescreen doctor
```

---

## Org map

| Repo | Role |
|------|------|
| [idle](https://github.com/idlescreen/idle) | Daemon, CLI, plugin host |
| [idle-tui](https://github.com/idlescreen/idle-tui) | Live terminal UI |
| [idle-cosmic](https://github.com/idlescreen/idle-cosmic) | COSMIC panel applet |
| [idle-saver-\*](https://github.com/orgs/idlescreen/repositories?q=idle-saver-) | Official effects |
| [packages](https://github.com/idlescreen/packages) | APT/DNF channel + installer |
| [render](https://github.com/idlescreen/render) / [idle-studio](https://github.com/idlescreen/idle-studio) | Offline export (preview) |
| idle-windows / idle-steam | **Stubs** (not shipping) |
| idle-pro | Strategy docs only |

---

## Links

- Website: [idlescreen.github.io](https://idlescreen.github.io)  
- Packages: [idlescreen.github.io/packages](https://idlescreen.github.io/packages/)  
