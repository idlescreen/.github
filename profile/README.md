# IdleScreen

**IdleScreen** is a high-performance ambient screensaver host and idle management suite designed for Wayland compositors (COSMIC, Hyprland, Sway, Wayfire, KDE Plasma Wayland).

---

## 🛠️ Package Repository Setup & Installation by OS

Before installing any IdleScreen products, you must first add the IdleScreen package repository to your operating system's package manager.

### 1. Fedora / RHEL / CentOS Stream (DNF)

#### Step 1: Install the DNF Repository File
```bash
sudo curl -fsSL https://idlescreen.github.io/packages/rpm/idlescreen.repo \
  -o /etc/yum.repos.d/idlescreen.repo
```

#### Step 2: Update Metadata & Install Package
```bash
# Refresh DNF package metadata
sudo dnf check-update

# Install main COSMIC desktop applet & daemon
sudo dnf install idle-cosmic

# Optional: Install live TUI controller
sudo dnf install idle-tui
```

---

### 2. Debian / Ubuntu / Pop!_OS / Linux Mint (APT)

#### Step 1: Add the GPG Keyring & APT Source
```bash
# Create keyrings directory
sudo mkdir -p /etc/apt/keyrings

# Download signed GPG keyring
sudo curl -fsSL https://idlescreen.github.io/packages/apt/idlescreen-keyring.gpg \
  -o /etc/apt/keyrings/idlescreen.gpg

# Add IdleScreen APT source
echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/idlescreen.gpg] https://idlescreen.github.io/packages/apt stable main" \
  | sudo tee /etc/apt/sources.list.d/idlescreen.list
```

#### Step 2: Update Package Database & Install Package
```bash
# Update APT index
sudo apt update

# Install main COSMIC desktop applet & daemon
sudo apt install idle-cosmic

# Optional: Install live TUI controller
sudo apt install idle-tui
```

---

### 3. Arch Linux / Manjaro / EndeavourOS

#### Step 1: Clone Package Definitions
```bash
git clone https://github.com/idlescreen/packages.git
cd packages/arch
```

#### Step 2: Build & Install with `makepkg`
```bash
makepkg -si
```

---

### 4. Nix / NixOS

#### Run Directly via Nix Flakes:
```bash
nix run github:idlescreen/packages#idle-cosmic
```

#### Build with Nix:
```bash
nix-build https://github.com/idlescreen/packages/archive/main.tar.gz -A idle-cosmic
```

---

### 5. Flatpak (Cross-Distro)

```bash
git clone https://github.com/idlescreen/packages.git
cd packages/flatpak
flatpak-builder --user --install --force-clean build-dir io.github.idlescreen.idle.yaml
```

---

## 📦 Products Overview

| Product | Description | Primary Repository |
|---------|-------------|--------------------|
| **`idle`** | Wayland screensaver daemon & host engine | [idlescreen/idle](https://github.com/idlescreen/idle) |
| **`idle-cosmic`** | COSMIC Desktop applet & integration | [idlescreen/idle-cosmic](https://github.com/idlescreen/idle-cosmic) |
| **`idle-tui`** | Interactive live Terminal User Interface | [idlescreen/idle-tui](https://github.com/idlescreen/idle-tui) |
| **`packages`** | Signed APT/DNF indexes & OS packaging | [idlescreen/packages](https://github.com/idlescreen/packages) |

---

🌐 **Web Portal:** [idlescreen.github.io/packages](https://idlescreen.github.io/packages/)
