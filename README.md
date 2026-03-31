## Artix-Installation-Guide-For-Dummies
This is a guide to how to install Artix Linux for people who cant read the f****** wiki.
This is for DINIT ONLY.
## Preparation
# 1) Assuming u have downloaded Artix Linux Base image, log in as root 
- `user : root` `password: artix`
You HAVE to log in as root or u wont be able to perform most of the stuff and wont be able to install the system.
# 2) Set ur your keyboard layout
An example for Polish layout `loadkeys pl` 
# 3) Partitioning
To partition your disk from scratch type `cfdisk -z /your/disk`
Choose GPT for UEFI systems.
- Create one `1GB EFI Partiton` // 1 GB IS recommended
Aswell as 
-`4-8GB Swap Partition [Linux Swap] ` // Completly optional 
And the rest reserve for `Linux Filesystem`.
# Now the formatting part.
- mkfs -F 32 /Your/Efi/Partition, for example `/dev/sda1/`
- mkswap /your/swap/partition, for example `/dev/sda2` // If u have created one.
- Enable it with `swapon /dev/sda2` or the partition u have swap formatted
- mkfs.ext4 /your/root/partiton, for example `/dev/sda3`
## 4) Mounting the partitions 
- mount /your/dev/sda2 /mnt  // For this tutorial i assume u have `/dev/sda2` as your Linux filesystem
- mkdir -p /mnt/boot/efi // This will create Partition for EFI
- mount /dev/sda1 /mnt/boot/efi  // Assuming u have `/dev/sda1` as your partition
## 5) Connecting to the Internet 
- Type `nmtui` if your using wifi,skip if your using ethernet.
- `ping google.com` to check if you have internet connection, `CTRL + C` to Stop.
## 6) Basestrapping the system
Perform this command do basestrap neccesary packages to your new system.
- `basestrap /mnt base base-devel linux linux-firmware dinit networkmanager networkmanager-dinit elogind elogind-dinit nano`
## 7) Creating Fstab
- `genfstab -U /mnt >> /mnt/etc/fstab`
## 8) Chrooting
Okay, now you are going to work on your freshly installed system
- `artix-chroot /mnt`
## 9) Time Zone
Symlink Your time zone to he system.
- `ln -sf /usr/share/zoneinfo/Europe/Warsaw /etc/localtime` // An Example For poland
Update your hardware clock
`hwclock --systohc`
## 10) Locale
Edit the locale using nano that you have installed ealier
Uncomment The one you want to use, Example for Polland will be `pl_PL.UTF8`
- `nano /etc/locale.gen`
Now generate the locale 
- `locale-gen`
As well as set them OS-Wide
- `echo "LANG=pl_PL.UTF-8" > /etc/locale.conf` // For english it will be `en_US.UTF8`
## 11) Hostname
Use this command to output your hostname into your system
- `echo "nya" > /etc/hostname` // nya is just an example, set any hostname you want :3
## 12) Root Password
- `passwd` [Your new Password]
## 12.5)
Ive forgot but i recommend at this part typing
- `mkinitcpio -p`
## 13) Bootloader
In this tutorial im going with GRUB,to install it use 
- `pacman -S grub efibootmgr`
Now propably the hardest part, install the grub into your system, the hardest one beacause you can mess it pretty easly...
- `grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB`
Now generate the config for grub 
- `grub-mkconfig -o /boot/grub/grub.cfg`
## 14) Enabling network manager since you are still in chroot
To start networkmanager on dinit type
- `dinitctl enable networkmanager`
## 15) Finish
Type `Exit` to quit from the chroot and then unmount your disks 
- `umount -R /mnt`
## 16) CONGRATULATIONS YOU HAVE SUCCESFULLY INSTALLED ARTIX LINUX
Now you can enjoy your freshly installed system, for troubleshooting the issues visit Artix Wiki:
- https://wiki.artixlinux.org/Main/Troubleshooting











