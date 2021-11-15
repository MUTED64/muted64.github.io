---
title: ArchLinux下奇怪小问题的解决
date: 2020-10-18 13:08:43
tags:
  - Linux
categories:
  - 系统
---

ArchLinux 配合KDE Plasma作为一个桌面系统来使用，相当好用。不过也有一些痛点和痒点需要自己踩坑。把踩坑过程放在这里整理一下。持续更新(~~大概~~)

<!-- more -->

## Plasma-discover 无法联网

### 问题描述

装了个比较纯净的Arch系统，没有安装kde-applications包组，发现plasma-discover无法联网，显示`Unable to load applications. Please verify Internet connectivity`。

### 解决方案

问题的产生是由于缺少kde-application包组里的一些包。不过这个包组实在臃肿，我不愿意装它。所以去定位造成问题的包了。最后发现造成问题的包是 `packagekit-qt5` 和 `appstream` 这两个。所以：

```bash
sudo pacman -S packagekit-qt5 appstream
```

问题解决。

## 双系统情况下Windows磁盘无法自动挂载

### 问题描述

我使用了Arch和Win10的双系统。每次需要访问Win10硬盘分区中的内容都要手动重新挂载而且需要用户密码。懒狗觉得这样不行，必须把这个问题解决了才能叫做懒狗。

### 解决方案

首先解决每次都需要手动挂载的问题。我先让它来个自动挂载。先查看一下分区看看自己需要挂载哪些内容：

```bash
fdisk -l
```

我需要挂载的是两个分区，一个是 `/dev/nvme0n1p3` （C盘），一个是 `/dev/nvme0n1p5` （D盘）。所以编辑一下fstab的内容：

```bash
sudo vim /etc/fstab
```

在文件的最后加上这样几句：

```
# /dev/nvme0n1p3
/dev/nvme0n1p3          /run/media/muted/Windows/  ntfs        rw,relatime     0 1

# /dev/nvme0n1p5
/dev/nvme0n1p5          /run/media/muted/DATA/      ntfs        rw,relatime     0 1
```

当然，具体情况具体分析，~~意思就是你照抄上面这份估计一般不行（~~

接下来解决需要输入密码的问题。STFW之后发现这个密码的配置在 `/usr/share/polkit-1/actions/org.freedesktop.UDisks2.policy
 ` 这里。备份一下：

```
sudo cp /usr/share/polkit-1/actions/org.freedesktop.UDisks2.policy org.freedesktop.UDisks2.policy.bak
```

改一下文件里 `<action id="org.freedesktop.udisks2.filesystem-mount-system">` 的相关配置，把 `<allow_active>` 标签里的 `auth_admin_keep` 改成 `yes` 。我的是在173行，当然这也需要具体情况具体分析了。

重启一下，发现这两块分区自动挂载到了/run/media/下面。成了！