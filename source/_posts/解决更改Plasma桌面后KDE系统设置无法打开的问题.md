---
title: 解决更改Plasma桌面后KDE系统设置无法打开的问题
date: 2020-09-11 11:31:32
tags:
- KDE
- Linux
categories:
- 系统
---

更新 Manjaro Linux(KDE) 的 Plasma 主题包之后发现系统设置无法打开的解决方案。

<!-- more -->

# 问题描述

在 Manjaro Linux 下配置 KDE Plasma 的时候更换了McMojave 主题。更换之后发现系统设置无法打开。猜测是由于主题的冲突导致的，于是猜测用切换成默认主题的方式可以解决此问题。但是一般更换默认主题的方式都是从系统设置入手，找到外观选项再切换主题，现在系统设置的 GUI 界面无法打开，于是尝试用其它方法更换成默认主题。

# 失败的尝试

## 用中文搜索相关内容

什么也没有。中文使用地区对操作系统问题的相关支持还是太薄弱了，即使有相关内容也一般都是及其过时的只言片语，相反转而去搜索英文内容的时候发现相关支持非常多，还是差距挺大的。也算获得了经验，以后遇到问题宁可去啃英文的文档也要减少对中文的依赖。

## 删除KDE设置项并重启

根据网络上的相关支持，将 `~/` 目录下的 `~/.kde/` 和 `~/.kde4/` 目录删除并重启，重启后发现 `~/.kde/` 目录自动生成了，其中的配置文件是根据当前主题的设置自动生成的。遂放弃。

## 重新安装 plasma-desktop 和 plasma-meta

重新安装之后发现没有任何变化。

# 成功的尝试

## 用命令行检查系统设置GUI程序的问题

Konsole 中键入 `systemsettings5` 以尝试从命令行启动系统设置。发现输出错误：

```bash
cyclic dependency detected between "file:///usr/lib/qt/qml/org/kde/kirigami.2/styles/org.kde.desktop.plasma/units.qml" and "file:///usr/lib/qt/qml/org/kde/kirigami.2/styles/org.kde.desktop.plasma/units.qml"
file:///usr/share/kpackage/genericqml/org.kde.systemsettings.sidebar/contents/ui/subcategorypage.qml:141:9: qml connections: implicitly defined onfoo properties in connections are deprecated. use this syntax instead: function onfoo(<arguments>) { ... }
file:///usr/share/kpackage/genericqml/org.kde.systemsettings.sidebar/contents/ui/subcategorypage.qml:131:9: qml connections: implicitly defined onfoo properties in connections are deprecated. use this syntax instead: function onfoo(<arguments>) { ... }
file:///usr/lib/qt/qml/org/kde/kirigami.2/private/refreshablescrollview.qml:143:13: qml connections: implicitly defined onfoo properties in connections are deprecated. use this syntax instead: function onfoo(<arguments>) { ... }
file:///usr/lib/qt/qml/org/kde/kirigami.2/private/refreshablescrollview.qml:143:13: qml connections: implicitly defined onfoo properties in connections are deprecated. use this syntax instead: function onfoo(<arguments>) { ... } 
cyclic dependency detected between
"file:///usr/lib/qt/qml/org/kde/kirigami.2/styles/org.kde.desktop.plasma/units.qml" and
"file:///usr/lib/qt/qml/org/kde/kirigami.2/styles/org.kde.desktop.plasma/units.qml"
qqmlengine::setcontextforobject(): object already has a qqmlcontext qt.qpa.xcb:
qxcbconnection: xcb error: 5 (badatom), sequence: 629, resource id: 0, major code: 20 (getproperty), minor code: 0
```

似乎确定了问题是由于新采用的主题产生了循环依赖之类的问题。接下来考虑如何将主题更换回默认。

## 从命令行将主题更换回 Breeze（默认主题）

使用 Konsole，键入 `systemsettings5 --style=Breeze`，绕过GUI界面从命令行直接更改主题。重启。系统设置可以打开，问题得到解决。

详请参见：https://www.reddit.com/r/kde/comments/i0m5gz/system_settings_wont_open_after_changing/

# 感想

中文社区的力量还是比较渺小，学英语真的很重要，英文社区啥都有（