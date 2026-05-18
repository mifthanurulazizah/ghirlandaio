konek internet
timedatctl
partisi
fdisk -l
Mirror
```
nano /etc/pacman.d/mirrorlist
```
packages penting
```
pacstrap -K /mnt base linux linux-firmware amd-ucode networkmanager nano sudo grub efibootmgr os-prober dosfstools mtools ntfs-3g man-db man-pages texinfo sof-firmware
```
fstab
```
genfstab -U /mnt >> /mnt/etc/fstab
```
chroot
```
arch-chroot /mnt
```
set time
```
ln -sf /usr/share/zoneinfo/Asia/Jakarta /etc/localtime
hwclock --systohc
```

localization
```
pacman -S nano
```
masuk ke folder
```
nano /etc/locale.gen
```
hapus # di 
```
LANG=en_US.UTF-8
```
```
locale -gen
```

ketik
```
nano /etc/locale.gen
```
ketik
```
LANG=en_US.UTF-8
```
hostname
```
nano /etc/hostname
```

ketik nama hostname

network management
pastikan network interface telah dilist dan dienable
```
ip a
ping archlinux.com
```
instal networkmanager
```
pacman -S networkmanager
```
start networkmanager
```
systemctl enable NetworkManager.service
```

initframs
```
mkinitcpio -P
```
root password
```
passwd
```
boot loader
install efibootmgr
```
pacman -S grub efibootmgr
```

