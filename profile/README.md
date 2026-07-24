# IdleScreen

**IdleScreen** is a modular, high-performance ambient screensaver host and idle management suite designed for Wayland compositors (COSMIC, Hyprland, Sway, Wayfire, KDE Plasma Wayland).

> 💡 **Standard Package:** `idlescreen` is the universal, recommended package for all desktop environments and Wayland compositors.  
> 🪐 **COSMIC Desktop Only:** `idle-cosmic` is specifically built for the COSMIC Desktop Environment applet integration.

---

## ⚡ Quick Install (Universal)

Run this single command in your terminal on Fedora, RHEL, Ubuntu, Debian, or Pop!_OS:

```bash
curl -fsSL https://idlescreen.github.io/packages/install.sh | sh
```

---

## 🛠️ Manual Repository Setup & Installation by OS

If you prefer to manually add the repository before installing via `dnf` or `apt`:

<details>
<summary><b>Fedora / RHEL / CentOS Stream (DNF)</b></summary>

<br>

#### Step 1: Add the IdleScreen DNF Repository
```bash
sudo curl -fsSL https://idlescreen.github.io/packages/rpm/idlescreen.repo \
  -o /etc/yum.repos.d/idlescreen.repo
```

#### Step 2: Refresh Metadata & Install Product
```bash
# Refresh DNF package database
sudo dnf check-update

# Install standard package (Universal for all DEs / Wayland compositors)
sudo dnf install idlescreen

# Launch TUI controller
idlescreen tui

# Optional (COSMIC DE Only): Install COSMIC desktop integration & applet
sudo dnf install idle-cosmic
```

</details>

<details>
<summary><b>Debian / Ubuntu / Pop!_OS / Linux Mint (APT)</b></summary>

<br>

#### Step 1: Download GPG Key & Add APT Repository
```bash
# Create keyrings directory
sudo mkdir -p /etc/apt/keyrings

# Download and install GPG keyring
sudo curl -fsSL https://idlescreen.github.io/packages/apt/idlescreen-keyring.gpg \
  -o /etc/apt/keyrings/idlescreen.gpg

# Add IdleScreen APT repository source
echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/idlescreen.gpg] https://idlescreen.github.io/packages/apt stable main" \
  | sudo tee /etc/apt/sources.list.d/idlescreen.list
```

#### Step 2: Update Package Database & Install Product
```bash
# Update APT package index
sudo apt update

# Install standard package (Universal for all DEs / Wayland compositors)
sudo apt install idlescreen

# Launch TUI controller
idlescreen tui

# Optional (COSMIC DE Only): Install COSMIC desktop integration & applet
sudo apt install idle-cosmic
```

</details>

<details>
<summary><b>Arch Linux / Manjaro / EndeavourOS (`makepkg`)</b></summary>

<br>

#### Step 1: Clone Package Definitions
```bash
git clone https://github.com/idlescreen/packages.git
cd packages/arch
```

#### Step 2: Build & Install Packages
```bash
# Build and install the standard IdleScreen package
makepkg -si
```

</details>

<details>
<summary><b>Flatpak (Cross-Distribution)</b></summary>

<br>

#### Step 1: Clone Manifests & Build
```bash
git clone https://github.com/idlescreen/packages.git
cd packages/flatpak
flatpak-builder --user --install --force-clean build-dir io.github.idlescreen.idle.yaml
```

</details>
