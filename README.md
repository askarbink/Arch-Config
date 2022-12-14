# Arch Linux Installation Guide (Outdated)

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

To check:
```sh
timedatectl status
```

<br>

To show partition types:
```sh
fdisk -l
```

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

To help:
```sh
	m
```

<br>

**To make filesystems:**
```sh
mkfs.fat -F 32 /dev/$efi_system
mkfs.btrfs -L $label /dev/$linux_file_system -f
```

```sh
mount -o compress=lzo /dev/$linux_file_system /mnt
mkdir /mnt/boot
mount /dev/$efi_system /mnt/boot
```

*If doing dual boot:*
```sh
mkdir /mnt/c /mnt/d
mount /dev/$windows_c /mnt/c
mount /dev/$windows_d /mnt/d
```

*If doing swap:*
```sh
mkswap /dev/$linux_swap
swapon /dev/$linux_swap
```

<br>

*For you safety, you may execute this command:*
```sh
pacman -Sy archlinux-keyring
```

<br>

**To install the minimal system:**
```sh
pacstrap -K /mnt base linux-zen linux-firmware
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
**Packages:** `base-devel` `efibootmgr` `grub` `linux-zen-headers` `neovim` `networkmanager`
<br>
If AMD CPU: `amd-ucode`
<br>
If Intel CPU: `intel-ucode`
<br>
*If doing dual boot:* `ntfs-3g` `os-prober`
<br>
*Archive:* `dosfstools` `mtools`
<br>

*If there’s an error while installing packages, try this:*
```sh
rm -r /etc/pacman.d/gnupg/
```
```sh
pacman-key --init
```
```sh
pacman-key --populate archlinux
```
```sh
pacman -Sc
```
```
pacman -Syyu
```

<br>

**To autostart Wi-Fi:**
```sh
systemctl enable NetworkManager
```

<br>

**To configure localizations:**
```sh
nvim /etc/locale.gen
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
echo LANG=en_US.UTF-8 > /etc/locale.conf
```

<br>

**To set the hostname:**
```sh
echo $hostname > /etc/hostname
```

<br>

**To configure hosts:**
```sh
nvim /etc/hosts
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

**To create a user and add them to the wheel group:**
```sh
useradd -mG wheel $username
```
To set their password:
```sh
passwd $username
```
```sh
$password
$password
```

To check the user groups:
```
userdbctl groups-of-user $username
```

<br>

**To configure the wheel group rights:**
```sh
EDITOR=nvim visudo
```
Remove `# ` before `%wheel ALL=(ALL:ALL) ALL`.

<br>

*To configure the bootloader:*
```sh
nvim /etc/default/grub
```
*If you want to skip the bootloader screen, you may set `GRUB_TIMEOUT` to `0`.*
<br>
*If using Wayland under an NVIDIA GPU, add `nvidia_drm.modeset=1` to `GRUB_CMDLINE_LINUX_DEFAULT`.*
<br>
*If doing dual boot, set `GRUB_DEFAULT` to `saved` and remove `#` before `GRUB_DISABLE_OS_PROBER=false`.*

<br>

**To install the bootloader:**
```sh
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=$bootloader_id
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

**To install `paru`:**
```sh
sudo pacman -S git
cd /tmp
git clone https://aur.archlinux.org/paru.git
cd yay
makepkg -si
```

<br>

*If installing GNOME:*
```sh
paru -S mutter-rounded
```

<br>

**All needed packages:**
```sh
paru -S $packages
```
**Audio:** `gst-plugin-pipewire` `pipewire-alsa` `pipewire-jack` `pipewire-pulse` `wireplumber`
<br>
If AMD GPU: `libva-mesa-driver` `vulkan-radeon` `xf86-video-ati`
<br>
If NVIDIA GPU: `nvidia-dkms` `nvidia-settings`
<br>
GNOME: `gnome` `gnome-browser-connector` `gnome-tweaks`
<br>
KDE Plasma: `ghostscript` `kdeconnect` `libdbusmenu-glib` `libdbusmenu-gtk2` `libdbusmenu-gtk3` `libdbusmenu-qt5` `libdbusmenu-qt6` `plasma` `plasma-wayland-session` `print-manager`
<br>
KDE Applications: `ark` `dolphin` `elisa` `gwenview` `haruna` `konsole` `skanlite` `spectacle` `youtube-dl`
<br>
*If needed Bluetooth:* `bluez`
<br>
*If needed to use printers:* `cups`
<br>
*Appearance:* `bibata-cursor-theme` `papirus-icon-theme` `ttf-google-sans` `ttf-firacode-nerd` `ttf-times-new-roman` `ttf-unifont`
<br>
*Additional packages:* `android-tools` `flatpak` `gdb` `neofetch` `python-pip`
<br>
*Applications:* `authy` `discord` `google-chrome` `obs-studio` `onlyoffice-bin` `telegram-desktop` `visual-studio-code-bin` `zoom`
<br>
*Other:* `bombsquad` `minecraft-launcher` `obinskit` `openrazer-meta`

<br>

*If installed `bluez`:*
```sh
sudo systemctl enable bluetooth
```
*If installed `cups`:*
```sh
sudo systemctl enable cups
```

<br>

*If installed GNOME:*
```sh
sudo systemctl enable gdm
```
*If installed KDE Plasma:*
```sh
sudo systemctl enable sddm
```

<br>

**To boot into the system:**
```sh
reboot
```

*If doing dual boot:*
```sh
timedatectl set-local-rtc true
```
