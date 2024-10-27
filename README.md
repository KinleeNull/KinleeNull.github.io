# KinleeNull.github.io
# Arch Linux Install
Download Arch ISO and check the SHA256 hash, which can be check with:

```shell
Get-FileHash 'filename'
 ```
Create new VM with ISO and give 2 GB of RAM and 20 GB of HDD space

Edit VM file and add:
```shell
firmware="efi"
```
Start up VM
## Boot
Verify the boot mode (64 is the anticipated response when using UEFI):
```shell
cat /sys/firmware/efi/fw_platform_size
```
Check Network
```
ping example.org
```
## Partitions
### Creating Partitions
```shell
fdisk /dev/sda
```
Once typing in this command, you will get several prompts to answer. 

For first partition:
```shell
n #for new partition
p #for primary partition
*hit enter* #for default partition number and starting sector
+500M #partition size
t #partition type
ef #makes it an EFI
```

For second partition:
```shell
n #for new partition
p #for primary partition
*hit enter* #for default partition number, starting sector, and to use the remaining of the disk
```

Then, to write your commands and exit fdisk:
```script
w
```
### Format Partitions
For first partition (EFI):
```shell
mkfs.fat -F 32 /dev/sda1
```
For second partition (root):
```shell
mkfs.ext4 /dev/sda2
```
### Mount file systems
For EFI partition:
```shell
mount --mkdir /dev/sda1 /mnt/boot
```
For root partition:
```shell
mount /dev/sda2 /mnt
```
## Installation
### Packages
Install essential packages:
```shell
pacstrap -K /mnt base linux linux-firmware networkmanager
```
Problem: Didn't install networkmanager. Had to install with:
```shell
pacman -Sy networkmanager
```
## Configuration
Generate an fstab file:
```
genfstab -U /mnt >> /mnt/etc/fstab
```
Change root:
```shell
arch-chroot /mnt
```
### Time
Set time zone:
```shell
ln -sf /usr/share/zoneinfo/America/Dallas /etc/localtime
```
Generate /etc/adjtime
```shell
hwclock --systohc
```
### Localization
Install nano:
```shell
pacman -S nano
```
```shell
nano /etc/locale.gen
```
- Once in nano, uncomment "en_US.UTF-8 UTF-8"
    * Use control+f to easily search for it

Then, run the following for the configuration file:
```shell
echo "LANG=en_US.UTF-8" > /etc/locale.conf
```
### Network Configuration
```shell
echo "CYB3353" > /etc/hostname
```
Change the root password:
```shell
passwd 
```
### Bootloader
Install grub bootloader:
```shell
pacman -Sy grub efibootmgr
```
```shell
grub-install --target=x86_64-efi --efi-directory=boot --bootloader-id=GRUB
```
```shell
grub-mkconfig -o /boot/grub/grub.cfg
```
## Reboot
Exit chroot:
```shell
exit
```
Reboot:
```shell
reboot 
```
## Install Desktop Environment (lxqt)
Enable and start networkmanager:
```shell
systemctl enable NetworkManager
```
```shell
systemctl start NetworkManager
```
Install xorg:
```shell
pacman -S xorg
```
Install lxqt and packages:
```shell
pacman -S lxqt lxqt-qtplugin xdg-user-dir ttf-freefont
```
Install and enable lightdm display manager:
```shell
pacman -S lightdm lightdm-gtk-greeter
```
```shell
systemctl enable lightdm
```
Install applications:
```shell
pacman -S firefox vlc filezilla leafpad archlinux-wallpaper
```
Install helpful tools:
```shell
pacman -S gtkmm3 xf86-vido-vmware xf86-input-vmmouse
```
```shell
pacman -S open-vm-tools
```
```shell
systemctl enable vmtoolsd
```
### Create Users
For each user, run these commands, replacing 'user':
```shell
useradd -m -G wheel -s /bin/bash user
```
```shell
passwd user
```
Then reboot:
```shell
reboot
```
This should boot you into the lxqt desktop environment where you will login with created user. Open up a terminal.
## Editing Desktop Environment
### Sudo Users
To allow sudo users to use sudo:
```shell
su -
```
```shell
pacman -s sudo
```
Open up visudo and uncomment the line that contains "%wheel ALL=(ALL) ALL":

```shell
EDITOR=nano visudo
```
Logout then log back in to be able to use sudo.
### Zsh
Install zsh:
```shell
sudo pacman -S zsh
```
### Install ssh 
```shell
sudo pacman -S openssh
```
### Create Aliases
These are the aliases I chose:
```shell
alias ll='ls-la'
```
#shows detailed file listing
```shell
alias install='sudo pacman -S'
```
```shell
alias ..='cd ..'
```
### Customization
- To customize terminal, go to File, Preferences, then Appearances to select a color rscheme for the terminal.

- To customize desktop, go to Preferences, Appearance, and adjust settings to your liking.
