# My personal notes on how to install Arch Linux.

**NB: This is not a tutorial to be followed by others.**

## Enable Wi-Fi if necessary:
```
iwctl
```
Use ```device list``` to find the name of the wireless adapter.

To scan for networks:
```
station DEVICE_NAME scan
```
To list their SSIDs:
```
station DEVICE_NAME get-networks
```
To connect to the desired network:
```
station DEVICE_NAME connect NETWORK_SSID
```

## Set the time to update automatically:
```
timedatectl set-ntp true
```

## Pick a drive and partition it (```/dev/sda``` usually):
To list all drives:
```
fdisk -l
```
Choose the drive:
```
fdisk /dev/sda
```
Use ```m``` for help.

Create ```GPT``` table for **UEFI** and ```DOS``` table for **BIOS/Legacy** installations.

UEFI: Create 550 MB EFI partition and 2 GB Swap partition.

Legacy: Create only a 2GB Swap partition.


## Create the filesystems on the partitions just created:
EFI:
```
mkfs.fat -F32 /dev/sda1
```
Swap:
```
mkswap /dev/sda2
```
```
swapon /dev/sda2
```
Root:
```
mkfs.ext4 /dev/sda3
```

## Mount the root partition:

```
mount /dev/sda3 /mnt
```

## Install necessary packages to root partition:

```
pacstrap /mnt base linux linux-firmware vim
```

## Generate the filesystem table file (fstab):

```
genfstab -U /mnt >> /mnt/etc/fstab
```

## Log in as root:

```
arch-chroot /mnt
```

## Select the local time zone:

```
ln -sf /usr/share/zoneinfo/Asia/Baku /etc/localtime
```

## Synchronize system and hardware (BIOS) clock (not in a vm):

```
hwclock --systohc
```

## Select the input language by editing ```/etc/locale.gen``` and uncommenting:
```
en_US.UTF-8 UTF-8
```

## Generate the locale file:

```
locale-gen
```

## Create ```/etc/hostname``` file:

* Enter: ```grindle-arch```
* Save and exit

## Edit ```/etc/hosts``` file:

* Append:
```
127.0.0.1    localhost
::1          localhost
127.0.1.1    grindle-arch.localdomain    grindle-arch
```
* Save and exit

## Create a root password:

```
passwd
```

## Create a user:

```
useradd -m grindle
```

## Create a password for the user:

```
passwd grindle
```

## Add the user to all the required groups:

```
usermod -aG wheel,audio,video,optical,storage grindle
```

## Install ```sudo``` package to grant root access when prompted:

```
pacman -S sudo
```

## Edit the configuration file for ```sudo```:

```
visudo
```
* Uncomment the first ```%wheel``` line

* Save and exit

## Install ```grub``` package and dependencies

```
pacman -S grub efibootmgr dosfstools os-prober mtools
```

## In case of UEFI installs - Create and mount the EFI directory:

```
mkdir /boot/EFI
```
```
mount /dev/sda1 /boot/EFI
```

## Install the GRUB bootloader:
**UEFI**:
```
grub-install --target=x86_64-efi --bootloader-id=grub_uefi --recheck
```
**BIOS/Legacy**:
```
grub-install --target=i386-pc /dev/sda
```
Create configuration file:
```
grub-mkconfig -o /boot/grub/grub.cfg
```

## Install the optional packages:

```
pacman -S archlinux-keyring git networkmanager wget
```

## Enable the Network Manager:

```
systemctl enable NetworkManager
```

## Exit root:

```
exit
```

## Unmount the root directory:

```
umount -l /mnt
```

## Reboot the system (shutdown and detach setup iso if in a vm):

```
reboot now
```

## Install the desktop environment:

```
sudo pacman -S gnome gnome-tweaks gdm
```
Select all default values
```
sudo systemctl enable gdm
```
```
sudo reboot now
```
