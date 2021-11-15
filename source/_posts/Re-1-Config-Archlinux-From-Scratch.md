---
title: 'Re:1 Config Archlinux From Scratch'
date: 2020-10-17 18:16:32
tags:
	- Linux
categories:
	- 系统
---

上一篇文章介绍了Archlinux的安装流程。但是，现在连个图形界面也没有，一堆效率导向的包都没有装上，这还玩个🔨啊。这篇来看看如何把Arch配置成用起来超爽的工具。因为到这里还没有装好中文输入法，所以和上一篇一样是英文的。英语水平不高，有错误的话，万分抱歉。~~下次重装系统自己能看懂就行~~

<!-- more -->

## Create a new user

It is unsafe to use root for daily use. It is better to create a ordinary user.

Let's create a new user named muted.

```bash
useradd -m -G wheel muted
```

`-m` stands for creating a new folder whose name is the same as your new user. You can use `~` to access it easily. `-G wheel` stands for adding the new user "muted" to a group named "wheel".

Set a password for this user:

```bash
passwd muted
```

## Config sudo

```bash
ln -s /usr/bin/vim /usr/bin/vi
visudo
```

Uncomment `# %wheel ALL=(ALL)ALL`, then reboot. This time your new user is able to use `sudo` command.

## Graphical interface installation

```bash
dhcpcd  # Connet to the Internet
sudo pacman -S xf86-video-intel  # If your GPU is from Intel
```

See also: https://wiki.archlinux.org/index.php/Xorg#Driver_installation

Now install Xorg and some necessary packages:

```bash
pacman -S xorg xorg-server plasma dolphin kate kdialog konsole fsearch dragon ffmpegthumbs sddm sddm-kcm
sudo systemctl enable sddm
```

`kde-meta-kdeadmin` `kde-meta-kdeutils` `kde-meta-kdegraphics` have been removed because of obsoleteness.

## Config your Internet

```bash
sudo systemctl disable netctl
sudo systemctl enable NetworkManager  # Draw attention on UPPER and lower case
sudo pacman -S network-manager-applet
```

## Use pacman mirror

```bash
sudo vim /etc/pacman.d/mirrorlist
```

Copy the mirrors you wants to use and paste them to the beginning of this file. One of the commonly used mirrors is `https://mirrors.tuna.tsinghua.edu.cn`.

I chose `https://linux.xidian.edu.cn/mirrors/archlinux/$repo/os/$arch` (Only available under the campus network environment).

## Use archlinuxcn repository

```bash
sudo vim /etc/pacman.conf
```

At the end of file add:

```bash
[archlinuxcn]
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```

If you are a 'Xidianer' too, you can also:

```bash
Server = https://linux.xidian.edu.cn/mirrors/archlinuxcn/$arch
```

Then:

```bash
sudo pacman -Syy
sudo pacman -Syu
sudo pacman -S archlinuxcn-keyring
```

## Use AUR repository with yay

```bash
sudo pacman -S yay
yay -Syu --devel --combinedupgrade --save
```

Then change current `aururl` to `https://aur.tuna.tsinghua.edu.cn`.

## Use fish shell as  your default shell

```bash
sudo pacman -S fish
```


Set fish the default shell on Konsole: `Konsole -> Settings -> Edit Current Profile`, choose /bin/fish as default command, Then restart Konsole.

Use fish as default user shell:

```bash
sudo chsh -s /usr/bin/fish root
sudo chsh -s /usr/bin/fish [username]
```

Set greeting words:

```bash
set fish_greeting 'Your Greeting Words'
```

Config fish shell:

```bash
fish_config
```

I recommend to set some abbreviations, for example:

`pc` is short for `proxychains`

`pmc` is short for `sudo vim /etc/pacman.d/mirrorlist`

`pmi` is short for `sudo pacman -S`

`pms` is short for `sudo pacman -Ss`

`pmu` is short for `sudo pacman -Syyu`

## Change hosts (Github)

Seems not very useful for me but... just record it here in case that some day I need it.

```bash
sudo pacman -S dnsutils
```


Use `dig` or `nslookup` command to build a hosts file yourself.

## Bypass GFW

```bash
sudo pacman -S v2ray qv2ray proxychains-ng
```

More? ...sorry but I won't tell you more🤪

**Caution! : Set your system time correctly first, or you can never success.**

If you want some rules, look at here: https://github.com/Loyalsoldier/v2ray-rules-dat, just put geosite and geoip into `/usr/share/v2ray/`. Easy!

## Fonts config

```bash
sudo pacman -S noto-fonts noto-fonts-cjk noto-fonts-emoji ttf-sarasa-gothic nerd-fonts-jetbrains-mono wqy-microhei wqy-zenhei
```


Download the config file at https://bitbucket.org/szclsya/dotfiles/src/master/fontconfig/fonts.conf, put it into `~/.config/fontconfig/fonts.conf`, log out and log in, then it works.

## Install Google-chrome

```bash
sudo pacman -S google-chrome
```

Caution: the bin file named `google-chrome-stable`, so if you want to start it use command line, use:

```bash
google-chrome-stable
```

## Chinese input

I chose Rime(fcitx5) as my Chinese input scheme instead of sogou pinyin because the latter has some strange conflict with Jetbrains IDEs. ~~🔪🔪🔪Fuck Sogou~~

```bash
sudo pacman -S fcitx5-im fcitx5-configtool
sudo pacman -S fcitx5-chinese-addons fcitx5-gtk fcitx5-qt fcitx5-pinyin-zhwiki kcm-fcitx5
sudo pacman -S fcitx5-rime
```

`fcitx-configtool -> add rime`

Now let's config it:

```bash
vim ~/.xprofile
```

Then add:

```bash
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE-fcitx
export XMODIFIERS="@im=fcitx"
fcitx5 &
```

Log out then Log in. It works. But... It's not BEEEAAAUTIFUL enough! 

**RXnb! Let's use RX's skin for fcitx5 input method!**
https://github.com/Reverier-Xu/RxWe10-Fcitx5
Put those theme file to `/usr/share/fcitx5/themes/`, and set `~/.config/fcitx5/conf/classicui.conf`:

```bash
# 垂直候选列表
Vertical Candidate List=False

# 按屏幕 DPI 使用
PerScreenDPI=True

# Font (设置成你喜欢的字体)
Font="字体字体字体字体写在这里"

# 主题
Theme=RxWe10-light
```

Or you can use some famous theme such as:

```bash
sudo pacman -S fcitx5-material-color
```

See Also: https://github.com/hosxy/Fcitx5-Material-Color

If you want to use simplified Chinese, create a new file named `default.custom.yaml` under `$HOME/.local/share/fcitx5/rime/` . With `default.yaml` in `./build` folder as reference, you can customize your settings freely. Also, you can turn to `/usr/share/rime-data/` to do those things.

## Install Jetbrains IDEs

Developers necessity. Use `pacman` or `yay` .

## Install Tencent shits

```bash
sudo vim /etc/pacman.conf
```

To use deepin-wine I have to enable multilib since my Archlinux is x64 version (?)

```bash
sudo pacman -Sy deepin.com.qq.office
```

Now TIM is installed. But we cannot run it now since it is designed to be compatible with GTK style desktops. So we:

```bash
sudo pacman -S gnome-settings-daemon
```

`System Settings -> Start up and shut down -> Autostart -> Add Script -> /usr/lib/gsd-xsettings`

Reboot. Now TIM is available. Though it is not easy to use as it is on Windows, we HAVE a TIM on Arch at least. 

## References

1. https://www.viseator.com/2017/05/19/arch_setup/
2. https://www.wootec.top/2019/11/02/%E5%A6%82%E4%BD%95%E5%9C%A8Linux%E4%B8%8A%E4%BC%98%E9%9B%85%E7%9A%84%E5%A0%95%E8%90%BD/
3. https://www.wootec.top/2019/11/15/%E5%A6%82%E4%BD%95%E5%9C%A8Linux%E4%B8%8A%E4%BC%98%E9%9B%85%E7%9A%84%E5%A0%95%E8%90%BD-2/
4. https://rocka.me/article/arch-linux-kde-plasma-install-and-config

