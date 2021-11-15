---
title: 'Re:0 Install Archlinux From Scratch'
date: 2020-10-17 12:32:00
tags:
	- Linux
categories:
	- 系统
---

前几天手贱把自己原来的Manjaro系统搞炸了，于是趁这个机会重装了自己一直心心念念的Arch，也算是从零开始的Arch生活了吧。因为这篇博客是在装系统的过程中写的，当时还没有配置中文的输入法，所以用了英文来写。英文水平有限肯定会出一些错误，还请海涵。~~自己再装的时候能看懂就行~~

<!-- more -->

## Prepare an installation medium

I used rufus on Windows 10 to make my U-disk an installation medium. I chose dd mode of rufus. Make sure there is an empty partition on your disk.

I will go straight to the next step because this is easy.

## Pre-Installation

### Boot the live environment

Disable secure boot. Then restart your PC and boot from your USB device. Choose `Boot Arch Linux (x86_64)` , now you can see Archlinux is ready to load contents. Finally you will logged in on the first virtual console as the root user, and presented with a `zsh` shell prompt. You can autocomplete your commands using `tab` , OHHHHHH!

### Verify the boot mode

To verify the boot mode, list the efivars directory:

```bash
ls /sys/firmware/efi/efivars
```

If the command shows the directory without error, then the system is booted in UEFI mode. If the directory does not exist, the system may be booted in BIOS (or CSM) mode. If the system did not boot in the mode you desired, refer to your motherboard's manual.

Or, a more secure way is `fdisk -l` ,if your SSD (or HDD) shows its `Disklabel type` is `gpt` and there is a small partition whose type is `EFI System` , Then your boot mode can be verified to be EFI.

### Connect to the Internet

Arch needs network connection to complete its installation.

Ensure your network interface is listed and enabled:

```bash
ip link
```

For wired connection:

```bash
dhcpcd
```

For wireless connection:

```bash
iwctl
device list
station wlan0 scan
station wlan0 get-networks
station wlan0 connect `xxxxx`
```

Check if you are connected to the internet:

```bash
ping baidu.com
```

If everything is OK, quit iwd:

```bash
quit
```

### Update the system clock

```bash
timedatectl set-ntp true
```

To check the service status, use `timedatectl status`.

### Partition the disks

| Device Type       | File Name                    |
| ----------------- | ---------------------------- |
| SATA/SAS/USB      | /dev/sda,/dev/sdb,……         |
| IDE               | /dev/hd0,/dev/hd1,……         |
| VIRTIO-BLOCK      | /dev/vda,/dev/vdb,……         |
| M2(SSD)           | /dev/nvme0,/dev/nvme1        |
| SD/MMC/EMMC(Card) | /dev/mmcblk0,/dev/mmcblk1    |
| CD drive          | /dev/cdrom,/dev/sr0,/dev/sr1 |

When recognized by the live system, disks are assigned to a [block device](https://wiki.archlinux.org/index.php/Block_device) such as `/dev/sda`, `/dev/nvme0n1` or `/dev/mmcblk0`. To identify these devices, use `lsblk` or `fdisk`.

I boot my Arch in UEFI mode, so first I must create a boot partition (EFI system partition). 

#### Create a boot partition

```bash
fdisk /dev/{YOUR DISK}  # Example: fdisk /dev/sdx  or  fdisk /dev/nvmex
```

See usages of commands by typing `m` and enter.

Enter `n` to create a new partition. You are asked to select the starting sector, generally we type enter (select by default). Then enter end sector or partition size. I used `+512M` .

Enter `p` to check if it works. Enter `t` to change the type of your new partition. Enter `l` to see all types supported. Change it to EFI.

Enter `w` to save. You can check whether there is something wrong before that by `p`.

Format it:

```bash
mkfs.fat -F32 /dev/{YOUR BOOT PARTITION}
```

#### Root partition

```bash
fdisk /dev/{YOUR DISK}
```

Create a new partition table if there is not one exist. I already have one on my disk so I go on directly.

Enter `n` to create a new partition. I want my new partition fill up the free space completely, so I use enter to choose the default.

Enter `p` then `w` just like creating your boot partition.

Format it:

```bash
mkfs.ext4 /dev/P{YOUR ROOT PARTITION}
```

### Mount the file systems

```bash
mount /dev/{YOUR ROOT PARTITION} /mnt
mkdir /mnt/boot
mount /dev/{YOUR BOOT PARTITION} /mnt/boot
```

## Installation

### Use Pacman Mirror

```bash
vim /etc/pacman.d/mirrorlist
```

Copy the mirrors you wants to use and paste them to the beginning of this file. One of the commonly used mirrors is `https://mirrors.tuna.tsinghua.edu.cn`.

I chose `https://linux.xidian.edu.cn/mirrors/archlinux/$repo/os/$arch` (Only available under the campus network environment).

### Install essential packages

```bash
pacstrap /mnt base base-devel linux linux-firmware dhcpcd
```

## Configure the system

### Fstab

```bash
genfstab -L /mnt >> /mnt/etc/fstab
```

We need a check to make sure it is right since it is an important step. Check the resulting `/mnt/etc/fstab` file, and edit it in case of errors:

```bash
cat /mnt/etc/fstab
```

### Chroot

`chroot` means 'change root'. Change root into the new system:

```bash
arch-chroot /mnt
```

We can also use this command to fix our system if there is some problem with it.

### Time zone

```bash
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
hwclock --systohc
```

### Install necessary packages

```bash
pacman -S vim dialog wpa_supplicant ntfs-3g networkmanager netctl
```

### Localization

```bash
vim /etc/locale.gen
```

Uncomment `zh_CN.UTF-8 UTF-8` `zh_HK.UTF-8 UTF-8` `zh_TW.UTF-8 UTF-8` `en_US.UTF-8 UTF-8` .

Then `:wq` save and quit.

Generate the locales by running:

```bash
locale-gen
```

Config locale file:

```bash
vim /etc/locale.conf
```

Add the following line to the first line of this file:

```bash
LANG=en_US.UTF-8
```

`:wq` . Fine.

### Network configuration

Create the hostname file:

```bash
vim /etc/hostname
```

Config your hosts:

```bash
vim /etc/hosts
```

Add the following contents to the end of this file:

```bash
127.0.0.1	localhost
::1		localhost
127.0.1.1	myhostname.localdomain	myhostname
```

### Set root password

```bash
passwd
```

### Install Intel-ucode (if you have a Intel CPU)

```bash
pacman -S intel-ucode
```

### Install BootLoader

Caution! This blog is only for EFI/GPT boot mode! If you are in BIOS/MBR mode, please see `Reference 2` at the end.

```bash
pacman -S os-prober ntfs-3g
pacman -S grub efibootmgr
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=grub
grub-mkconfig -o /boot/grub/grub.cfg
```

If there is multi systems on your device, it is recommended to check whether each system's entry is well generated.

```bash
vim /boot/grub/grub.cfg
```

Check if it is all right.

### Reboot and enjoy archlinux

```bash
exit
umount /mnt/boot
umount /mnt
reboot
```

Pull out your U-disk. Login as root, enter your password.

If you can see the command line, congratulations, you have installed Archlinux successfully! OHHHHHHH!

There is still many things to do after that, I will put some guide for them on my next blog.

## References

1. https://wiki.archlinux.org/index.php/Installation_guide

2. https://www.viseator.com/2017/05/17/arch_install/
