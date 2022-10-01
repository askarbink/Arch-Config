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

## Connecting to Wi-Fi

```sh
iwctl
```

```sh
    device list
    station $device scan
    station $device get-networks
    station $device connect $ssid
    $passphrase
```

<br>

To check connection:
```sh
    station $device show
```

<br>

```sh
    exit
```

<br>
If it’s unnable to connect, try this before:

```sh
rfkill unblock wifi
```

<br>

## Pre-Installation

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

**To generate the file system table:**
```sh
genfstab -U /mnt >> /mnt/etc/fstab
```

<br>

**To jump into the system:**
```sh
arch-chroot /mnt
```

<br>

## …

“timedatectl list-timezones | grep $location” to show timezones.

ln -sf /usr/share/zoneinfo/Asia/Yekaterinburg /etc/localtime

hwclock --systohc

pacman -S
  /*
    bluez
    cups
    mtools
    ntfs-3g
    os-prober
  */

    $cpu-ucode
    base-devel
    dosfstools
    efibootmgr
    grub
    linux-zen-headers
    networkmanager
    vim

systemctl enable bluetooth
systemctl enable cups
systemctl enable NetworkManager

vim /etc/locale.gen

Remove “#” before “en_US.UTF-8 UTF-8”.

locale-gen

echo "LANG=en_US.UTF-8" > /etc/locale.conf

echo "$hostname" > /etc/hostname

vim /etc/hosts
“““
127.0.0.1 localhost
::1 localhost
127.0.1.1 $hostname.localdomain $hostname
”””

passwd
$password
$password

useradd -m $username
passwd $username
$password
$password

usermod -aG wheel $username

“userdbctl groups-of-user $username” to check.

EDITOR=vim visudo
Remove “# ” before “%wheel ALL=(ALL:ALL) ALL”.

vim /etc/default/grub

Add “nvidia_drm.modeset=1” to “GRUB_CMDLINE_LINUX_DEFAULT”. 

Remove “#” before “GRUB_DISABLE_OS_PROBER=false”.

grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id="Arch Linux"

grub-mkconfig -o /boot/grub/grub.cfg

exit
umount -a
reboot

sudo os-prober

sudo grub-mkconfig -o /boot/grub/grub.cfg

sudo nmtui

sudo pacman -Syyu

sudo pacman -S
    nvidia-dkms
    nvidia-settings
  or
    egl-wayland
    libva-mesa-driver
    vulkan-radeon
    xf86-video-ati

    flatpak
    git
    gst-plugin-pipewire
    neofetch
    pipewire-alsa
    pipewire-pulse
    python-pip

sudo systemctl enable $dm
