Let's start. 

This assumes your target disk is /dev/sda, you are running UEFI, you are wired into ethernet for the install process, you use US keyboard, and mostly understand english

------- Partitioning

`cgdisk /dev/sda`

	/dev/sda1 200M		(ef00) /boot   
	/dev/sda2 $REST_OF_HDD	(8300) /     

```
mkfs.vfat -F 32 /dev/sda1
mkfs.ext4 /dev/sda2

mount /dev/sda2 /mnt
mkdir /mnt/boot
mount /dev/sda1 /mnt/boot
```

------- Actual install
Install all packages needed (add what you need!)

```
pacstrap /mnt base base-devel linux linux-firmware f2fs-tools nano xorg-server gnome gnome-extra firefox networkmanager htop zsh grml-zsh-config
genfstab -U /mnt >> /mnt/etc/fstab
```

------- Get in there to configure
```
arch-chroot /mnt /bin/zsh

echo "en_US.UTF-8 UTF-8" > /etc/locale.gen
echo LANG=en_US.UTF-8 > /etc/locale.conf

locale-gen
```
------- Users
```
passwd
chsh root -s /bin/zsh

useradd -m -G wheel -s /bin/zsh user
passwd user
```
------- Enable services

`systemctl enable NetworkManager gdm`

------- Bootloader

`bootctl install`

------- Kernel cmdline
`nano /boot/loader/entries/arch.conf`

```
title   Arch Linux
linux   /vmlinuz-linux
initrd  /initramfs-linux.img
options root=/dev/sda2 rw
```

------- Git out

`exit`
`reboot`

