---
title: 使用Optimus在ArchLinux下配置双显卡双显示器
date: 2021-04-05 22:05:12
tags:
- Linux
- 显卡
categories:
- 系统
---

ArchLinux使用过程中发现笔记本电脑双显卡和双显示器工作不正常，以下是个人的解决方案，参考ArchWiki，仅供参考。

<!-- more -->

## 环境

**OS**: Arch Linux x86_64

**Kernel**: 5.11.11-arch1-1 

**DE**: KDE Plasma 5.21.3

**GPU0**: NVIDIA GeForce GTX 1050 Mobile

**GPU1**: Intel UHD Graphics 630

**Monitor0**为笔电自带屏幕，**Monitor1**为HDMI连接的外接显示器。

## 问题

使用HDMI接入外接显示器，由于笔记本电脑自带的屏幕默认使用了Intel的核显，而HDMI接入的显示器会使用NVIDIA的显卡，造成禁用自带屏幕时的撕裂和渲染巨慢的问题。

## 解决方案

### 保证显卡驱动正常

#### Intel显卡驱动

```bash
$ sudo pacman -S mesa lib32-mesa
```

另外需要注意的是，如果之前安装了 `xf86-video-intel` 包，最好卸载掉。官方已经不建议继续使用此软件包。

>  Often not recommended, see note below
>
> **Note:** Some ([Debian & Ubuntu](https://www.phoronix.com/scan.php?page=news_item&px=Ubuntu-Debian-Abandon-Intel-DDX), [Fedora](https://www.phoronix.com/scan.php?page=news_item&px=Fedora-Xorg-Intel-DDX-Switch), [KDE](https://community.kde.org/Plasma/5.9_Errata#Intel_GPUs)) recommend not installing the [xf86-video-intel](https://www.archlinux.org/packages/?name=xf86-video-intel) driver, and instead falling back on the modesetting driver for Gen4 and newer GPUs (GMA 3000 from 2006 and newer). See [[1\]](https://web.archive.org/web/20160714232204/https://www.reddit.com/r/archlinux/comments/4cojj9/it_is_probably_time_to_ditch_xf86videointel/), [[2\]](https://www.phoronix.com/scan.php?page=article&item=intel-modesetting-2017&num=1), [Xorg#Installation](https://wiki.archlinux.org/index.php/Xorg#Installation), and [modesetting(4)](https://jlk.fjfi.cvut.cz/arch/manpages/man/modesetting.4). However, the modesetting driver can cause problems such as [Chromium Issue 370022](https://bugs.chromium.org/p/chromium/issues/detail?id=370022).

卸载方式：

```bash
$ sudo pacman -Rsc xf86-video-intel
```

#### Nvidia显卡驱动

安装 nvidia 包，为了对 32 位程序的支持，安装 lib32-nvidia-utils 包 (位于 multilib 仓库，需要在pacman config中提前自行开启此仓库)。

```bash
$ sudo pacman -S nvidia lib32-nvidia-utils
```

### 配置 PRIME render offload

安装 `nvidia-prime` 包来使用官方配置。

```bash
$ sudo pacman -S nvidia-prime
```

重启系统后可以使用 `prime-run` 命令使指定程序在N卡上渲染。例如

```bash
# 以 n 卡渲染 vlc
$ prime-run vlc
# 以 n 卡渲染 wine 游戏
$ prime-run wine touhou08.exe
```

可以使用 `mesa-demo` 包里的 `glxinfo` 指令查看当前使用的显卡。

### 使用Optimus Manager配置双显卡

此时HDMI连接的显示器可以正常工作了。但是笔电自带的显示器卡在TTY，可能是由于显卡不兼容的问题。我选择了使用Optimus Manager进行双显卡的配置。

#### 事前准备

首先清除（备份）`/etc/X11/xorg.conf.d/` 下的配置文件, 并删掉（备份）`/etc/X11/xorg.conf`, 因为Optimus Manager会自动生成配置文件存放到`/etc/X11/xorg.conf.d/`里面, 所以建议安装前把显示配置相关的文件都清除掉。

#### 安装

安装Optimus Manager包：

```bash
# Arch Linux CN
$ sudo pacman -S optimus-manager
# AUR
$ yay -S optimus-manager
```

如果你是GDM用户或者Manjaro KDE 用户，请务必参考[README](https://github.com/Askannz/optimus-manager#important--gnome-and-gdm-users)中的注意事项。

#### 配置

接下来修改配置文件。将 `/usr/share/` 目录下的配置文件放到 `/etc/optimus-manager/` 下进行设置。将切换方式设为`switching=none`, 不推荐使用bbswitch（容易遇到ACPI锁死的问题, [参考Wiki](https://wiki.archlinux.org/index.php/NVIDIA_Optimus#Lockup_issue_(lspci_hangs))）, 设置`pci_power_control=yes`让PCI Power Management切换显卡。

```bash
$ sudo cp /usr/share/optimus-manager.conf /etc/optimus-manager/optimus-manager.conf
$ sudo vim /etc/optimus-manager/optimus-manager.conf
```

根据开机需求自动选择显卡。

```vim
startup_mode=auto
startup_auto_battery_mode=integrated
startup_auto_extpower_mode=nvidia
```

上述配置设置了开机时选择使用的显卡。电池模式使用了集显，而充电时使用独显。Reboot之后双显卡显示正常，此时可以使用 `nvidia-settings` 对双显示器的显示情况进行设置。解决了之前的“laggy”和“tearing”的情况。

#### 使用

```bash
$ optimus-manager --switch nvidia #切换到nvidia显卡
$ optimus-manager --switch integrated #切换到集显
```

注意：

- 切换显卡的过程中会自动注销登录, 所以记得**保存并关掉电脑正在运行的程序**。
- 你可以在配置文件中修改`auto_logout=false`禁止自动注销以手动注销切换显卡。

## 其它

grub在哪块屏幕上显示的问题，之后补充。

## References

1. https://blog.sakuya.love/archives/linuxgpu/
2. https://blog.starry-s.me/posts/archlinux-pavilion-gaming-laptop/
3. https://wiki.archlinux.org/index.php/NVIDIA