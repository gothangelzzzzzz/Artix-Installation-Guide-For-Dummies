## Artix-Installation-Guide-For-Dummies
This is a guide on how to install Artix Linux for people who cant read the f****** wiki. 
This is for DINIT ONLY.
(I am not responsible if you break something, do it at your own risk)
## Preparation
# 1) Assuming you have downloaded Artix Linux Base image, log into the OS as root 
- `user : root` `password: artix`
You HAVE to log in as root or you won't be able to perform most of the stuff and won't be able to install the system.
# 2) Set ur your keyboard layout
An example for the Polish layout `loadkeys pl` 
# 3) Partitioning
----
Before that I'd recommend running `lsblk` to determine which drive you want to use (to install Artix onto it)
----

To partition your disk from scratch type `cfdisk -z /dev/yourDisk`
Choose GPT for UEFI systems.
- Create one `1GB EFI Partiton` // 1 GB IS recommended
Aswell as 
-`4-8GB Swap Partition [Linux Swap] ` // Completely optional, but without it the OS will crash/shutdown as soon as you run out of free RAM
And reserve the other free space for `Linux Filesystem`.
# Now the formatting part.
- mkfs -F 32 /Your/Efi/Partition, for example `/dev/sda1/` // fat32
- mkswap /your/swap/partition, for example `/dev/sda2` // If you've created one
- Enable it with `swapon /dev/sda2`
- mkfs.ext4 /your/root/partiton, for example `/dev/sda3` // ext4, could also use btrfs due to snapshot capabilites
## 4) Mounting the partitions
- mount /your/dev/sda2 /mnt // For this tutorial I'll assume you choose `/dev/sda2` as your Linux filesystem
- mkdir -p /mnt/boot/efi // This will create Partition for EFI, -p means create all of the mentioned folders if they don't already exist
- mount /dev/sda1 /mnt/boot/efi // Assuming you have `/dev/sda1` as your partition
## 5) Connecting to the Internet 
- Type `nmtui` if you're using wifi, skip if you're using ethernet.
- `ping google.com` to check if you have internet connection, `CTRL + C` to stop.
## 6) Basestrapping the system
Perform this command to basestrap necessary packages to your new system.
- `basestrap /mnt base base-devel linux linux-firmware dinit networkmanager networkmanager-dinit elogind elogind-dinit nano`
## 7) Generating Fstab
- `genfstab -U /mnt >> /mnt/etc/fstab`
## 8) Chrooting
Okay, now you're going to work on your freshly installed system
- `artix-chroot /mnt`
## 9) Time Zone
Symlink your time zone to the /etc/localtime
- `ln -sf /usr/share/zoneinfo/Europe/Warsaw /etc/localtime` // An example for Poland
Update your hardware clock
`hwclock --systohc`
## 10) Locale
Edit the locale using nano that you have installed ealier
Uncomment the one you want to use, an example for Poland will be `pl_PL.UTF8`
- `nano /etc/locale.gen`
Now generate the locale 
- `locale-gen`
As well as set it OS-Wide
- `echo "LANG=pl_PL.UTF-8" > /etc/locale.conf` // For English it will be `en_US.UTF8` ; > means overwrite, >> means appending text to the end of the file
## 11) Hostname
Use this command to set the hostname in your system
- `echo "nya" > /etc/hostname` // nya is just an example, set any hostname you want :3
## 12) Root Password
- `passwd` [Your new Password]
## 12.5)
I've forgot but at this part I recommend to type in this command: 
- `mkinitcpio -p` // basically it creates "initial ramdisk", which provides necessary modules to the kernel at startup and more
## 13) Bootloader
In this tutorial I am going to use GRUB, to install it use 
- `pacman -S grub efibootmgr`
Now probably the hardest part - installing the GRUB into your system, the hardest one because you can mess it up pretty easily...
- `grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB`
Now generate the config for grub 
- `grub-mkconfig -o /boot/grub/grub.cfg`
## 14) Enabling NetworkManager since you are still in chroot
To start networkmanager on dinit type
- `dinitctl enable networkmanager` // pretty similar to systemd, which uses systemctl
## 15) Finish
Type `Exit` to quit from the chroot and then unmount your disks 
- `umount -R /mnt`
## 16) CONGRATULATIONS YOU HAVE SUCCESFULLY INSTALLED ARTIX LINUX
Now you can enjoy your freshly installed system, for troubleshooting the issues visit Artix Wiki:
- https://wiki.artixlinux.org/Main/Troubleshooting











