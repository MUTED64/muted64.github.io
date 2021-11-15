---
title: fcitx5-rime配置指南
date: 2021-11-13 23:11:50
tags:
  - 系统
  - 配置
categories:
  - 系统
---

Rime 作为 Linux 下相当不错的输入法，对 fcitx5 的文档支持倒是很差劲。本文介绍了 fcitx5-rime 配置的一些小细节，防止多次踩坑。

<!-- more -->

## fcitx5-rime 的配置文件目录

从 fcitx4 升级到 fcitx5，Rime 悄悄把它的配置文件目录从 `~/.config/fcitx/rime/` 改到了 `~/.local/share/fcitx5/rime/` ，网络上倒是有一堆拿 fcitx4-rime 的配置路径冠名到 fcitx5 上的，那怎么可能能行得通呢（

## fcitx5-rime 如何手动修改配置

fcitx5-rime 的配置目录中有一个 `build` 文件夹，其中的各个以 `.schema.yaml` 结尾的文件是在更新时会自动覆盖掉的默认配置文件。我们当然可以手动去更改这些文件使新配置生效，但是每次更新之后配置都会被覆盖掉。rime 提供的解决方案是，我们可以在 fcitx5-rime 配置文件夹中创建一个 `.custom.yaml` 的配置文件，每次我们需要再次 deploy 输入法的时候，rime 会首先查找是否有 custom 配置文件，如果没有则再回退到 schema 上。

rime 的配置文件均使用 yaml 格式，关于 yaml 格式的详情请搜索相关详情。鉴于 rime 在 deploy 时如果 yaml 文件有格式错误并不会报错，因此可能出现看似写好了配置但并没有生效的情况，需要仔细检查 yaml 格式是否有误。

rime 为自定义配置提供了 `patch` 选项，用于在 schema 配置上修改需要 patch 的值，并保留 schema 中的其它配置，非常方便。

## 配置默认使用英文和简体字的用例

```yaml
# ~/.local/share/fcitx5/rime/luna_pinyin.custom.yaml

patch:
  switches:
    - name: ascii_mode
      reset: 1
      states: ["中文", "西文"]
    - name: full_shape
      states: ["半角", "全角"]
    - name: zh_simp
      reset: 1
      states: ["漢字", "汉字"]
    - name: ascii_punct
      states: ["。，", "．，"]
```

需要注意简体字有的版本写作 zh_simp，有的版本则是 simplification，使用时可以尝试一下，不行再换另一个。
