# **Nirvic Arch Guide**

This guide explains how I install Arch Linux for my daily system. The setup is fully manual for now because I have not finished building my complete dotfiles. I prefer keeping everything manual at this stage so the system stays clean, simple, and easy to adjust later.

I also maintain a minimal dotfiles setup called **niriha**, which is a lightweight configuration used as a temporary environment or a starting point before switching to the full Nirvic setup.

This guide covers how I prepare and configure my Arch system for **Niri**, **Hyprland**, and my overall workflow. I use different styles depending on what I need at the moment, including layout choices, theming, fonts, window manager behavior, and the general structure of the system.
The goal is to keep the system clean, customizable, and easy to switch between setups.

---

# **Table of Contents (Compact)**

1. [Overview](#components-overview)
2. [Arch Installation](#manual-installation-details)
3. [AUR Helper](#aur-helper-installation)
4. [Niri Setup](#niri-setup-guide)
5. [Noctalia-Shell](#noctalia-shell-install--setup-guide)
6. [Papirus Icon Theme](#papirus-icon-theme-guide)
7. [Wayland Fonts](#wayland-font-setup-guide-nirihyprland)
8. [OBS Studio](#obs-studio-install-guide-wayland)
9. [Launchers](#walker-launcher-install-guide)
10. [Spicetify](#spicetify-setup-guide)
11. [Polkit & Drive Access](#polkit-fix--secondary-drive-access)
12. [Spotify Scaling](#adjust-spotify-scaling-on-linux)
13. [Hyprland Monitor Setup](#hyprland-monitor-resolution-guide)
14. [LazyVim](#lazyvim-install-guide)
15. [Essential Apps](#essential-apps-my-apps)
16. [Setup Complete](#setup-complete)

---

## **Components Overview**

[Niri](https://github.com/YaLTeR/niri):
A modern Wayland compositor focused on a clean tiling experience, smooth animations, and a minimal configuration style. It is stable, lightweight, and optimized for daily workflows.

[Vicinae](https://github.com/noctalia-dev/vicinae):
A fast, elegant application launcher designed for Noctalia environments. It integrates with Wayland compositors and provides a clean, minimal interface for launching apps.

[Noctalia-Shell](https://github.com/noctalia-dev/noctalia-shell):
A highly customizable Wayland shell that manages UI elements, panels, widgets, and theming. It is designed to work seamlessly with Niri and other Wayland compositors for a smooth, aesthetic desktop experience.

---

## **Manual Installation Details**

You can install Arch Linux manually using the vanilla Arch ISO and the built-in `archinstall` script.

### Steps:

1. Download the Arch Linux ISO: [https://archlinux.org/download/](https://archlinux.org/download/)
2. Write it to a USB stick (use **balenaEtcher** on macOS/Windows)
3. Boot from the USB (disable **Secure Boot** in BIOS)
4. For Wi-Fi:

   ```bash
   iwctl
   station wlan0 scan
   station wlan0 connect <your network>
   ```

   Ethernet works automatically.
5. Run `archinstall` and follow the configuration table below:

| Section                        | Configuration Option                                                                                                    |
| ------------------------------ | ----------------------------------------------------------------------------------------------------------------------- |
| Mirrors and repositories       | Select regions → your country                                                                                           |
| Disk configuration             | **Option 1:** Use entire disk → erases all data<br>**Option 2:** Use existing partition → overwrites selected partition |
| Disk → File system             | btrfs (default structure + compression) or ext4 (no separate partitioning needed)                                       |
| Disk → Disk encryption         | No encryption, or LUKS + password + select partitions                                                                   |
| Hostname                       | Choose your system name                                                                                                 |
| Bootloader                     | systemd-boot / GRUB / Limine                                                                                            |
| Authentication → Root password | Set your root password                                                                                                  |
| Authentication → User account  | Add a user → grant superuser access → confirm                                                                           |
| Applications → Audio           | pipewire                                                                                                                |
| Network configuration          | Copy ISO network configuration                                                                                          |
| Timezone                       | Set your location                                                                                                       |

After installation:

* Reboot the system
* Log in using the user you created

---

# **AUR Helper Installation**

AUR helpers like **yay** and **paru** let you easily install and manage packages from the **Arch User Repository (AUR)**, including handling dependencies and updates.

---

## **Install yay**

```bash
sudo pacman -S --needed git base-devel
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si

# Usage
yay -S <package>   # install
yay -Syu           # update
```

## **Install paru**

```bash
sudo pacman -S --needed git base-devel
git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si

# Usage
paru -S <package>  # install
paru -Syu          # update
```

---

⚠️ **Warning:** No need to install both AUR helpers.

---

# Niri Setup Guide

This guide explains how to set up Niri as your daily driver on Arch-based systems. It includes installing Niri, essential applications, a launcher, and configuring auto-startup apps.

---

## 1. Access TTY

After booting, you will get a TTY prompt.

1. Enter your root username.
2. Enter your root password.
3. You will now be at a clean `tty1`.

This is where you will start the installation process.

---

## 2. Install Niri

You can install Niri using one of the following commands:

```
sudo pacman -S niri-git
# or
sudo pacman -S niri
```

This will install the Niri compositor, which we will use as the foundation for our setup.

---

## 3. Install Daily Applications

Here are the applications I install and use daily:

```
yay -S kitty fuzzzel swaybg otf-font-awesome ttf-jetbrains-mono xwayland-satellite
```

* **kitty**: Terminal emulator
* **fuzzzel**: Tool I use (replace with your preferred tool)
* **swaybg**: Background manager for Wayland
* **otf-font-awesome / ttf-jetbrains-mono**: Fonts for UI and coding
* **xwayland-satellite**: Wayland/X11 compatibility

---

## 4. Install and Enable SDDM

SDDM is a display manager. Install it:

```
yay -S sddm
```

Enable SDDM so it starts automatically:

```
sudo systemctl enable sddm --now
```

After this, SDDM will open. Enter your password, and then log in to Niri.

---

## 5. Configure Display in Niri

Once inside Niri, configure your display resolution in `config.kdl`. Example for a 1920x1080 monitor at 320Hz:

```
output "DP-1" {
    mode "1920x1080@319.998"
    scale 1
}
```

* Replace `"DP-1"` with your output name (use `wlr-randr` or `swaymsg -t get_outputs` to find it).
* Replace `1920x1080@319.998` with your own resolution and refresh rate.

---

## 6. Add Auto-Startup Applications

You can configure Niri to automatically start applications when it launches:

```
spawn-at-startup "elephant"
spawn-at-startup "waybar"
spawn-at-startup "mako"
```

* **elephant**: Example app (replace with what you need)
* **waybar**: Status bar for Wayland
* **mako**: Notification daemon

Add any other applications you want to start automatically.

---

## 7. Install More Daily Applications

Here are other applications I use daily:

```
yay -S spotify-launcher zed vesktop-bin neovim firefox obs-studio
```

* **spotify-launcher**: Spotify client
* **zed**: Editor
* **vesktop-bin**: Virtual desktop manager
* **neovim**: Text editor
* **firefox**: Browser
* **obs-studio**: Screen recording/streaming

---

## 8. Install a Launcher

I use **Vicinae** as my launcher. Install it using:

```
yay -S vicinae-bin
# or
curl -fsSL https://vicinae.com/install.sh | bash
```

---

## 9. Niri Configuration for Vicinae

To make Vicinae start automatically and work properly with Niri, add the following to `~/.config/niri/config.kdl`:

```
spawn-sh-at-startup "vicinae server"

# Shortcut to toggle Vicinae
Mod+Space repeat=false { spawn "vicinae" "toggle"; }
```

* `spawn-sh-at-startup "vicinae server"`: Starts the launcher server automatically
* `Mod+Space`: Keybinding to toggle the launcher

Refer to Vicinae documentation for more customization options.

---


# Noctalia-Shell Install & Setup Guide


## 1. Install Noctalia-Shell

```bash
yay -S noctalia-shell-git
```

## 2. Enable and Start Service

```bash
systemctl --user enable --now noctalia.service
```

## 3. Verify Service

```bash
systemctl --user status noctalia.service
```

## 4. Configure Noctalia-Shell

```bash
cp -r /etc/noctalia-shell ~/.config/noctalia-shell
```

Edit `~/.config/noctalia-shell/config.kdl` to set display, keybindings, and startup apps:

```kdl
spawn-at-startup "waybar"
spawn-at-startup "mako"
spawn-at-startup "kitty"
```

---


# Papirus Icon Theme Guide

This guide explains how to **install, apply, customize colors, and uninstall** the Papirus Icon Theme.

---

## 1. Install Papirus Icon Theme

Use `yay` to install:

```bash
yay -S papirus-icon-theme
```

---

## 2. Apply the Theme

* Open your **desktop environment’s appearance/settings**.
* Go to **Icons** and select **Papirus**.

Optional: Choose **Papirus Dark** or **Papirus Light** variants.

---

## 3. Customize Folder Colors

Install the **Papirus folder color script**:

```bash
wget -qO- https://git.io/papirus-folders-install | env PREFIX=/usr/local sh
```

Change folder colors for a theme:

```bash
papirus-folders -C <color> --theme Papirus-Dark
```

* Replace `<color>` with your preferred color name (e.g., blue, red, orange).
* Replace `Papirus-Dark` with `Papirus-Light` if you use the light theme.

---

## 4. Uninstall Folder Color Script

To uninstall the folder color script:

```bash
wget -qO- https://git.io/papirus-folders-install | env uninstall=true sh
```

---

## Papirus colow Preview

| Name | Preview | Name | Preview |
|:-----|:-------:|:-----|:-------:|
| **adwaita** | ![folder-adwaita](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-adwaita.svg) ![user-adwaita-home](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/user-adwaita-home.svg) ![folder-adwaita-download](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-adwaita-download.svg) | **black** | ![folder-black](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-black.svg) ![user-black-home](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/user-black-home.svg) ![folder-black-download](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-black-download.svg) |
| **blue** | ![folder-blue](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-blue.svg) ![user-blue-home](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/user-blue-home.svg) ![folder-blue-download](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-blue-download.svg) | **bluegrey** | ![folder-bluegrey](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-bluegrey.svg) ![user-bluegrey-home](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/user-bluegrey-home.svg) ![folder-bluegrey-download](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-bluegrey-download.svg) |
| **breeze** | ![folder-breeze](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-breeze.svg) ![user-breeze-home](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/user-breeze-home.svg) ![folder-breeze-download](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-breeze-download.svg) | **brown** | ![folder-brown](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-brown.svg) ![user-brown-home](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/user-brown-home.svg) ![folder-brown-download](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-brown-download.svg) |
| **carmine** | ![folder-carmine](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-carmine.svg) ![user-carmine-home](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/user-carmine-home.svg) ![folder-carmine-download](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-carmine-download.svg) | **cyan** | ![folder-cyan](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-cyan.svg) ![user-cyan-home](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/user-cyan-home.svg) ![folder-cyan-download](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-cyan-download.svg) |
| **darkcyan** | ![folder-darkcyan](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-darkcyan.svg) ![user-darkcyan-home](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/user-darkcyan-home.svg) ![folder-darkcyan-download](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-darkcyan-download.svg) | **deeporange** | ![folder-deeporange](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-deeporange.svg) ![user-deeporange-home](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/user-deeporange-home.svg) ![folder-deeporange-download](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-deeporange-download.svg) |
| **green** | ![folder-green](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-green.svg) ![user-green-home](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/user-green-home.svg) ![folder-green-download](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-green-download.svg) | **grey** | ![folder-grey](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-grey.svg) ![user-grey-home](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/user-grey-home.svg) ![folder-grey-download](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-grey-download.svg) |
| **indigo** | ![folder-indigo](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-indigo.svg) ![user-indigo-home](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/user-indigo-home.svg) ![folder-indigo-download](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-indigo-download.svg) | **magenta** | ![folder-magenta](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-magenta.svg) ![user-magenta-home](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/user-magenta-home.svg) ![folder-magenta-download](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-magenta-download.svg) |
| **nordic** | ![folder-nordic](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-nordic.svg) ![user-nordic-home](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/user-nordic-home.svg) ![folder-nordic-download](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-nordic-download.svg) | **orange** | ![folder-orange](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-orange.svg) ![user-orange-home](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/user-orange-home.svg) ![folder-orange-download](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-orange-download.svg) |
| **palebrown** | ![folder-palebrown](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-palebrown.svg) ![user-palebrown-home](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/user-palebrown-home.svg) ![folder-palebrown-download](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-palebrown-download.svg) | **paleorange** | ![folder-paleorange](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-paleorange.svg) ![user-paleorange-home](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/user-paleorange-home.svg) ![folder-paleorange-download](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-paleorange-download.svg) |
| **pink** | ![folder-pink](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-pink.svg) ![user-pink-home](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/user-pink-home.svg) ![folder-pink-download](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-pink-download.svg) | **red** | ![folder-red](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-red.svg) ![user-red-home](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/user-red-home.svg) ![folder-red-download](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-red-download.svg) |
| **teal** | ![folder-teal](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-teal.svg) ![user-teal-home](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/user-teal-home.svg) ![folder-teal-download](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-teal-download.svg) | **violet** | ![folder-violet](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-violet.svg) ![user-violet-home](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/user-violet-home.svg) ![folder-violet-download](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-violet-download.svg) |
| **white** | ![folder-white](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-white.svg) ![user-white-home](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/user-white-home.svg) ![folder-white-download](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-white-download.svg) | **yaru** | ![folder-yaru](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-yaru.svg) ![user-yaru-home](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/user-yaru-home.svg) ![folder-yaru-download](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-yaru-download.svg) |
| **yellow** | ![folder-yellow](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-yellow.svg) ![user-yellow-home](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/user-yellow-home.svg) ![folder-yellow-download](https://cdn.jsdelivr.net/gh/PapirusDevelopmentTeam/papirus-icon-theme@master/Papirus/48x48/places/folder-yellow-download.svg) |




# Wayland Font Setup Guide (Niri/Hyprland)

---

## 1. Install Recommended Fonts

```bash
sudo pacman -S noto-fonts noto-fonts-emoji noto-fonts-extra
sudo pacman -S ttf-liberation ttf-roboto ttf-jetbrains-mono ttf-fira-code ttf-hack
yay -S ttf-symbola  # from AUR
```

---

## 2. Optional: Remove Default Fonts

If your native language fonts look bad:

```bash
yay -Rns ttf-dejavu gnu-free-font
```

---

## 3. Refresh Font Cache

```bash
fc-cache -fv
```

---

## 4. Reboot

Reboot your system to enjoy better fonts in Wayland.

---

# OBS Studio Install Guide (Wayland)

This guide installs **OBS Studio** for recording and streaming on Wayland setups (Niri/Hyprland) and configures **PipeWire** for proper screen capture.

---

## 1. Install OBS Studio

Install OBS Studio from the AUR:

```bash
yay -S obs-studio
```

This provides the main application for recording and streaming.

---

## 2. Install PipeWire and Dependencies

PipeWire handles screen capture on Wayland:

```bash
sudo pacman -S pipewire wireplumber xdg-desktop-portal xdg-desktop-portal-gnome
```

* `pipewire` – core service for audio/video routing.
* `wireplumber` – session manager for PipeWire.
* `xdg-desktop-portal` & `xdg-desktop-portal-gnome` – enable sandboxed apps to capture screens.

---

## 3. Enable Required Services

```bash
systemctl --user enable --now pipewire
systemctl --user enable --now wireplumber
systemctl --user enable --now pipewire-pulse
```

This ensures PipeWire and its services start automatically with your session.

---

## 4. Launch OBS

```bash
obs
```

OBS Studio is now ready for screen recording and streaming on Wayland.

---

# Walker Launcher Install Guide

> **Caution:** You do not need this if you are installing **Vicinae**.

---

## What is Walker?

[Walker](https://github.com/abenz1267/walker) is a lightweight, modular launcher for Linux.
It integrates with **Elephant** applications for daily tasks, provides fast app launching, search functionality, and system utilities.
It works well in Wayland compositors like Niri/Hyprland and is a minimal, efficient alternative to full-featured launchers.

---

## Features Comparison

|                                                    *Walker*                                                   |                                                       *Vicinae*                                                      |
| :-----------------------------------------------------------------------------------------------------------: | :------------------------------------------------------------------------------------------------------------------: |
| Lightweight, minimal, integrates with Elephant, modular launcher, fast app launching, simple Wayland support. | More feature-rich, integrates with Vicinae ecosystem, full launcher features, extended UI, advanced Wayland support. |

---

## Installation Guide

### 1. Install Elephant Suite

```bash
yay -S elephant elephant-bluetooth elephant-calc elephant-clipboard \
elephant-desktopapplications elephant-files elephant-menus elephant-providerlist \
elephant-symbols elephant-todo elephant-unicode elephant-websearch
```

### 2. Install Walker

```bash
yay -S walker
```

---

## Running Walker

Start Elephant as a user service to run Walker:

```bash
elephant service enable   # generates and enables systemd user service
systemctl --user start elephant.service  # start without reboot
```

Walker is now ready to use alongside Elephant applications.

---



# Spicetify Setup Guide

<table>
<tr>
<td>

<img src="https://github.com/spicetify/cli/blob/04155e776a2b9de9aeb547b993196c4967778b8f/.github/assets/logo.png" alt="Spicetify Logo" width="400px">

</td>
<td valign="top" style="padding-left: 30px; font-size: 16px; line-height: 1.5;">

Spicetify is a <b>command-line tool</b> to customize the official Spotify client.
It supports Windows, macOS, and Linux, allowing you to <b>change colors, inject CSS, extensions, and custom apps</b> to fully control the Spotify interface.

</td>
</tr>
</table>

---

## 1. Install Spicetify

```bash
curl -fsSL https://raw.githubusercontent.com/spicetify/cli/main/install.sh | sh
```

---

## 2. Apply Spicetify

```bash
spicetify backup apply
```

> Backs up your current Spotify configuration and applies Spicetify safely.

---

## 3. Fix `xpui.ini` Issues

```bash
find ~/.config/spotify -name prefs
nano ~/.config/spicetify/config-xpui.ini
```

Set:

```
prefs_path = /home/rakhal/.config/spotify/prefs
```

Then reapply:

```bash
spicetify backup apply
```

---

## Notes

* Run `spicetify backup apply` after changes.
* Reapply if Spotify updates.
* Works on Wayland compositors like **Niri/Hyprland**.

---

### Links

* [Official Docs](https://spicetify.app/docs/getting-started)
* [Basic Usage](https://spicetify.app/docs/getting-started#basic-usage)

---




# Polkit Fix & Secondary Drive Access

This guide helps fix Polkit authentication issues on your system, ensuring all administrative prompts work correctly, and enables smooth mounting and access of secondary drives.

---

## Install Polkit GNOME

Install the required Polkit package:

```bash
sudo pacman -S polkit-gnome
```

---

## Start Polkit Authentication Agent

Manually start the Polkit agent to enable authentication prompts:

```bash
/usr/lib/polkit-gnome/polkit-gnome-authentication-agent-1 &
```

---

## Enable Auto-start for Polkit

Ensure Polkit runs automatically on login:

```bash
mkdir -p ~/.config/autostart/
nano ~/.config/autostart/polkit-gnome-authentication-agent-1.desktop
```

Paste the following into the file:

```ini
[Desktop Entry]
Type=Application
Exec=/usr/lib/polkit-gnome/polkit-gnome-authentication-agent-1
Name=PolicyKit GNOME Authentication Agent
Comment=Authentication agent for PolicyKit
X-GNOME-Autostart-enabled=true
```

Save and exit.

Polkit will now run automatically, allowing proper mounting and access of secondary drives.

---



# Adjust Spotify Scaling on Linux

This guide helps fix scaling issues in Spotify by overriding the `.desktop` file. This method is recommended and does not require creating a wrapper script.

---

## Desktop File Override (Recommended)

### Step 1: Copy the Spotify `.desktop` File

Open a terminal and run:

```bash
mkdir -p ~/.local/share/applications
cp /usr/share/applications/spotify.desktop ~/.local/share/applications/spotify.desktop
```

This copies the Spotify launcher to your **local applications folder**, so changes won't affect system-wide settings.

---

### Step 2: Edit the Copied File

Open the copied file in a text editor:

```bash
nano ~/.local/share/applications/spotify.desktop
```

---

### Step 3: Modify the Exec Line

Locate the line that starts with:

```text
Exec=spotify %U
```

Change it to:

```text
Exec=env GDK_SCALE=1 GDK_DPI_SCALE=0.6667 spotify %U
```

This sets the scaling for Spotify correctly.

---

### Step 4: Save and Exit

* Press **Ctrl + O** to save.
* Press **Enter** to confirm.
* Press **Ctrl + X** to exit.

---

### Step 5: Launch Spotify

Now, any time you launch Spotify from your app launcher or menu, it will automatically use the correct scale. No wrapper script is needed.

---


# Hyprland Monitor Resolution Guide

**⚠️ Warning:** Do not follow these settings if you have your own Hyprland configuration. These are personal examples from an older setup and may not suit every system.

This guide explains how to configure monitor resolution and refresh rate in Hyprland.

---

## Step 1: Update Your System

Ensure your system is up to date:

```bash
sudo pacman -Syu
```

---

## Step 2: Configure Monitor Resolution

Open your monitor configuration file:

```bash
nano ~/.config/hypr/monitors.conf
```

Add or modify your monitor entry:

```text
monitor=DP-1,1920x1080@320,0x0,1
```

* **DP-1** – Replace with your monitor’s identifier.
* **1920x1080@320** – Set the desired resolution and refresh rate.
* **0x0** – Position of the monitor on the screen.
* **1** – Enable the monitor.

Save the file and exit.

---

## Step 3: Optional – Adjust Window Decorations

Edit the main Hyprland configuration:

```bash
nano ~/.config/hypr/hyprland.conf
```

Add or modify the decoration section:

```text
decoration {
    rounding = 12
}
```

This sets rounded corners for windows, giving a smoother appearance.

---

## Step 4: Apply Changes

Reload Hyprland to apply your new settings:

```bash
hyprctl reload
```

Your monitor should now run with the correct resolution and refresh rate.

---



# LazyVim Install Guide

## What is LazyVim

LazyVim is a pre-configured Neovim setup using lazy.nvim, providing fast editing, lazy-loaded plugins, and sensible defaults.

## Install

Backup existing config:

```bash
mv ~/.config/nvim{,.bak}
mv ~/.local/share/nvim{,.bak}
mv ~/.local/state/nvim{,.bak}
mv ~/.cache/nvim{,.bak}
```

Clone LazyVim starter:

```bash
git clone https://github.com/LazyVim/starter ~/.config/nvim
rm -rf ~/.config/nvim/.git
```

Launch Neovim to install plugins:

```bash
nvim
```

Check setup:

```vim
:LazyHealth
```

---



# Essential Apps (My Apps)

Install apps via `yay`:

```bash
yay -S pacseek gnome-calendar gnome-calculator gnome-disks gnome-disks-utility udisks \
telegram-desktop-bin vesktop-bin obs-studio-bin neovim nwg-look nwg-displays btop mpv vlc
```

---

## App Details

|                                   App                                  | Description                        |
| :--------------------------------------------------------------------: | :--------------------------------- |
|          [Pacseek](https://archlinux.org/packages/?q=pacseek)          | Terminal package manager frontend. |
|   [GNOME Calendar](https://archlinux.org/packages/?q=gnome-calendar)   | Track events & appointments.       |
| [GNOME Calculator](https://archlinux.org/packages/?q=gnome-calculator) | Standard/scientific calculator.    |
|   [GNOME Disks](https://archlinux.org/packages/?q=gnome-disk-utility)  | Manage disks & partitions.         |
|           [UDISKS](https://archlinux.org/packages/?q=udisks)           | Disk daemon & automount.           |
|  [Telegram](https://aur.archlinux.org/packages/telegram-desktop-bin/)  | Telegram client.                   |
|       [Vesktop](https://aur.archlinux.org/packages/vesktop-bin/)       | Minimal Wayland launcher.          |
|       [OBS Studio](https://archlinux.org/packages/?q=obs-studio)       | Record & stream video.             |
|           [Neovim](https://archlinux.org/packages/?q=neovim)           | Modern Vim fork.                   |
|         [nwg-look](https://aur.archlinux.org/packages/nwg-look)        | GTK theme & icon manager.          |
|     [nwg-displays](https://aur.archlinux.org/packages/nwg-displays)    | Wayland display config.            |
|             [btop](https://archlinux.org/packages/?q=btop)             | Terminal resource monitor.         |
|              [MPV](https://archlinux.org/packages/?q=mpv)              | Command-line media player.         |
|              [VLC](https://archlinux.org/packages/?q=vlc)              | Full-featured media player.        |

---

Got it. Here's a clean, aesthetic, minimal ending section without emojis:

---

# Setup Complete

Your system is now fully configured with essential tools, apps, and customizations.

You now have:

* A fully functional Wayland environment (Niri/Hyprland)
* Development tools and utilities installed and ready
* Media, productivity, and system management applications available
* Fonts, scaling, and theme improvements applied
* Launchers like Walker and customization tools like Spicetify set up

> Tip: Explore each application and tool to optimize it for your workflow. Keep your system updated regularly using `sudo pacman -Syu` and `yay -Syu` for AUR packages.

System setup is complete. You can now begin using your configured environment efficiently.






