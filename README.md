# **Nirvic Arch Guide**

This guide explains how I install Arch Linux for my daily system. The setup is fully manual for now because I have not finished building my complete dotfiles. I prefer keeping everything manual at this stage so the system stays clean, simple, and easy to adjust later.

I also maintain a minimal dotfiles setup called **niriha**, which is a lightweight configuration used as a temporary environment or a starting point before switching to the full Nirvic setup.

This guide covers how I prepare and configure my Arch system for **Niri**, **Hyprland**, and my overall workflow. I use different styles depending on what I need at the moment, including layout choices, theming, fonts, window manager behavior, and the general structure of the system.
The goal is to keep the system clean, customizable, and easy to switch between setups.

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

## Papirus Folders Colors Preview

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



