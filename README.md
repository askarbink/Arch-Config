# Arch Linux Installation Guide

<br>

To check file content:
```sh
cat $filename
```

<br>

To cancel an operation:
<br>
`Ctrl+C`

<br>

If qoutes (`"…"`) in commands only contain one word, you may omit them.

<br>

## Connecting to Wi-Fi

```sh
iwctl
```

To get all the available devices:
```sh
    device list
```
To scan and get all the available networks:
```sh
	station $device scan
	station $device get-networks
```
To connect to the network:
```sh
	station $device connect $ssid
```
```sh
	$passphrase
```

<br>

To check connection:
```sh
	station $device show
```

<br>

To exit:
```sh
	exit
```

<br>
If it’s unnable to connect, try this before:

```sh
rfkill unblock wifi
```

<br>

## Basic Installation

To check connection:
```sh
ping $sitename
```

<br>

**To syncronize time with NTP servers:**

```sh
timedatectl set-ntp true
```

<br>

To check:
```sh
timedatectl status
```

<br>

To show partition types:
```sh
fdisk -l
```

<br>

To show mountpoints:
```sh
lsblk
```

<br>

Partition space for EFI System (512M), Linux filesystem, *Linux swap and Microsoft basic data*.

<br>

To partition:
```sh
fdisk /dev/$disk
```

<br>

To help:
```sh
	m
```

<br>

**To make filesystems:**
```sh
mkfs.fat -F 32 /dev/$efisystem
mkfs.btrfs -L $label /dev/$linuxfilesystem -f
```

```sh
mount -o compress=lzo /dev/$linuxfilesystem /mnt
mkdir /mnt/boot
mount /dev/$efisystem /mnt/boot
```

<br>

*If doing dual boot:*
```sh
mkdir /mnt/c
mkdir /mnt/d
mount /dev/$windowsc /mnt/c
mount /dev/$windowsd /mnt/d
```

<br>

*If doing swap:*
```sh
mkswap /dev/$linuxswap
swapon /dev/$linuxswap
```

<br>

**To install the minimal system:**
```sh
pacstrap /mnt base linux-zen linux-firmware
```

<br>

**To generate a file system table:**
```sh
genfstab -U /mnt >> /mnt/etc/fstab
```

<br>

**To jump into the system:**
```sh
arch-chroot /mnt
```

<br>

To show timezones:
```sh
timedatectl list-timezones | grep $location
```

<br>

**To set the Yekaterinburg’s timezone:**
```sh
ln -sf /usr/share/zoneinfo/Asia/Yekaterinburg /etc/localtime
```

<br>

**To synchronize time with hardware clocks:**
```sh
hwclock --systohc
```

<br>

```sh
pacman -S $packages
```
**Packages:** `base-devel` `dosfstools` `efibootmgr` `grub` `linux-zen-headers` `networkmanager` `vim`
<br>
If AMD CPU: `amd-ucode`
<br>
If Intel CPU: `intel-ucode`
<br>
*If doing dual boot:* `mtools` `ntfs-3g` `os-prober`

<br>

**To autostart Wi-Fi:**
```sh
systemctl enable NetworkManager
```

<br>

**To configure localizations:**
```sh
vim /etc/locale.gen
```
Remove `#` before `en_US.UTF-8 UTF-8`.

<br>

**To generate the lozalization:**
```sh
locale-gen
```

<br>

**To set the system language:**
```sh
echo "LANG=en_US.UTF-8" > /etc/locale.conf
```

<br>

**To set the hostname:**
```sh
echo "$hostname" > /etc/hostname
```

<br>

**To configure hosts:**
```sh
vim /etc/hosts
```
Write here:
```
127.0.0.1	localhost
::1		localhost
127.0.1.1	$hostname.localdomain	$hostname
```

<br>

**To set the root password:**
```sh
passwd
```
```sh
$password
$password
```

<br>

**To add a user:**
```sh
useradd -m $username
```
To set their password:
```sh
passwd $username
```
```sh
$password
$password
```
To make them a super user:
```sh
usermod -aG wheel $username
```

To check the user groups:
```
userdbctl groups-of-user $username
```

<br>

**To configure the wheel group rights:**
```sh
EDITOR=vim visudo
```
Remove `# ` before `%wheel ALL=(ALL:ALL) ALL`.

<br>

*To configure the bootloader:*
```sh
vim /etc/default/grub
```
*If using Wayland under an NVIDIA GPU, add `nvidia_drm.modeset=1` to `GRUB_CMDLINE_LINUX_DEFAULT`.*
<br>
*If doing dual boot, remove `#` before `GRUB_DISABLE_OS_PROBER=false`.*

<br>

**To install the bootloader:**
```sh
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id="$bootloaderid"
```
```sh
grub-mkconfig -o /boot/grub/grub.cfg
```

<br>

**To boot into the system:**
```sh
exit
umount -a
reboot
```

<br>

## Installing Drivers and Desktop Environment

<br>

**Log in to your user account.**

<br>

*If doing dual boot:*
```sh
sudo os-prober
```
```sh
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

<br>

To connect to Wi-Fi:
```sh
sudo nmtui
```

<br>

To update everything:
```sh
sudo pacman -Syyu
```

<br>

**To install `yay`:**
```sh
sudo pacman -S git
cd /tmp
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```

<br>

```sh
yay -S $packages
```
**Audio:** `gst-plugin-pipewire` `pipewire-alsa` `pipewire-pulse`
<br>
If AMD GPU: `libva-mesa-driver` `vulkan-radeon` `xf86-video-ati`
<br>
If NVIDIA GPU: `nvidia-dkms` `nvidia-settings`
<br>
GNOME: `gdm` `gnome-browser-connector` `gnome-control-center` `gnome-keyring` `gnome-menus` `gnome-remote-desktop` `gnome-shell-extensions` `gnome-user-share` `gvfs-afc` `gvfs-goa` `gvfs-google` `gvfs-gphoto2` `gvfs-mtp` `gvfs-nfs` `gvfs-smb` `rygel` `xdg-user-dirs-gtk`
<br>
GNOME Applications: `eog` `file-roller` `gnome-calculator` `gnome-clocks` `gnome-font-viewer` `gnome-system-monitor` `gnome-terminal` `gnome-tweaks` `nautilus` `simple-scan` `sushi` `totem`
<br>
KDE Plasma: `kdeconnect` `libdbusmenu-glib` `libdbusmenu-gtk2` `libdbusmenu-gtk3` `libdbusmenu-qt5` `libdbusmenu-qt6` `plasma-meta` `plasma-wayland-session` `print-manager` `sddm`
<br>
KDE Applications: `ark` `dolphin` `elisa` `gwenview` `haruna` `konsole` `spectacle` `youtube-dl`
<br>
*If needed Bluetooth:* `bluez`
<br>
*If needed to use printers:* `cups`
<br>
*Appearance:* `bibata-cursor-theme` `papirus-icon-theme` `ttf-google-sans` `ttf-roboto-mono` `ttf-times-new-roman`
<br>
*Additional packages:* `android-tools` `flatpak` `gdb` `neofetch` `python-pip`
<br>
*Applications:* `authy` `discord` `google-chrome` `obs-studio` `telegram-desktop` `visual-studio-code-bin` `zoom`
<br>
*Other:* `bommbsquad` `minecraft-launcher` `obinskit` `openrazer-meta`

<br>

*If installed `bluez`*:
```sh
sudo systemctl enable bluetooth
```
*If installed `cups`*:
```sh
sudo systemctl enable cups
```

<br>

If installed GNOME:
```sh
sudo systemctl enable gdm
```
If installed KDE Plasma:
```sh
sudo systemctl enable sddm
```

<br>

**To boot into the system:**
```sh
reboot
```
