---
title: Tkinter学习笔记
date: 2020-06-16 19:43:31
tags:
- 开发
- python
- GUI
categories:
- 开发
---

简单的Python GUI... 要想强大一点还是去用PyQt了。

<!-- more -->

# 使用逻辑

1. import tkinter。

2. 实例化窗口对象。例如

   ``````
   window = tkinter.Tk()
   ``````

3. 对刚才实例化的窗口对象进行设置，如

   ```
   window.title('This is a titie.')
   window.geometry('200x100')   # 中间的符号是英文字母'x'
   ```

4. 实例化各窗口内的控件并将其放置在窗口的某个位置，例如

   ```
   # 实例化标签对象, 注意：方法的首字母大写
   label = tkinter.Label(window, text='', textvariable=..., font=('Consolas', 20, 'bold'), width=150, height=100)
   
   # 放置在窗口的某个位置
   label.pack()
   ```

5. 令主窗口自我刷新，完成GUI的交互作用，如

   ```
   window.mainloop()
   ```

   

# 简单控件的使用方法小结

* Label控件

  Label控件是tkinter中的标签控件。用于显示文本和图像。语法格式如下：

  ```
  label = tkinter.Label( master, options, ...)
  ```

  - master: 框架的父容器，可理解为某个窗口。

  - options: 可设置的属性。这些选项可以用键值对的形式设置，并以逗号分隔。

    以下是可选项清单：

    |  序号  |                        可选项 & 描述                         |
    | :----: | :----------------------------------------------------------: |
    |   1    | **anchor **    文本或图像在背景内容区的位置，默认为 center，可选值为（n,s,w,e,ne,nw,sw,se,center）eswn 是东南西北英文的首字母，表示：上北下南左西右东。 |
    |   2    |                     **bg**  标签背景颜色                     |
    |   4    |             **bd**  标签的大小，默认为 2 个像素              |
    |   4    |  **bitmap**  指定标签上的位图，如果指定了图片，则该选项忽略  |
    |   5    | **cursor ** 鼠标移动到标签时，光标的形状，可以设置为 arrow, circle, cross, plus 等。 |
    |   6    |                     **font ** 设置字体。                     |
    |   7    |                     **fg ** 设置前景色。                     |
    |   8    |             **height ** 标签的高度，默认值是 0。             |
    |   9    |                  **image ** 设置标签图像。                   |
    |   10   | **justify** 定义对齐方式，可选值有：LEFT,RIGHT,CENTER，默认为 CENTER。 |
    |   11   |            **padx**  x轴间距，以像素计，默认 1。             |
    |   12   |            **pady**  y轴间距，以像素计，默认 1。             |
    |   13   | **relief**  边框样式，可选的有：FLAT、SUNKEN、RAISED、GROOVE、RIDGE。默认为 FLAT。 |
    | **14** |           **text ** 设置文本，可以包含换行符(\n)。           |
    | **15** | **textvariable**  标签显示 Tkinter 变量，StringVar。如果变量被修改，标签文本将自动更新。 |
    |   16   | **underline ** 设置下划线，默认 -1，如果设置 1，则是从第二个字符开始画下划线。 |
    |   17   | **width ** 设置标签宽度，默认值是 0，自动计算，单位以像素计。 |
    |   10   |     **wraplength ** 设置标签文本为多少行显示，默认为 0。     |





* Button控件

  Button控件是tkinter中的按钮控件。用于在程序中显示按钮。按钮可与一个command函数进行关联，在按下按钮时执行command函数。语法格式如下：

  ```
  button = tkinter.Button ( master, option=value, ... )
  ```

  以下是可选项清单：

  | 序号  |                        可选项 & 描述                         |
  | :---: | :----------------------------------------------------------: |
  |   1   |      **activebackground**  当鼠标放上去时，按钮的背景色      |
  |   2   |      **activeforeground ** 当鼠标放上去时，按钮的前景色      |
  |   3   |           **bd ** 按钮边框的大小，默认为 2 个像素            |
  |   4   |                     **bg ** 按钮的背景色                     |
  | **5** |   **command**  按钮关联的函数，当按钮被点击时，执行该函数    |
  |   6   |            **fg ** 按钮的前景色（按钮文本的颜色）            |
  |   7   |                      **font ** 文本字体                      |
  |   8   |                    **height ** 按钮的高度                    |
  |   9   |               **highlightcolor ** 要高亮的颜色               |
  |  10   |                **image**  按钮上要显示的图片                 |
  |  11   | **justify ** 显示多行文本的时候,设置不同行之间的对齐方式，可选项包括LEFT, RIGHT, CENTER |
  |  12   | **padx **  按钮在x轴方向上的内边距(padding)，是指按钮的内容与按钮边缘的距离 |
  |  13   |          **pady ** 按钮在y轴方向上的内边距(padding)          |
  |  14   | **relief ** 边框样式，设置控件3D效果，可选的有：FLAT、SUNKEN、RAISED、GROOVE、RIDGE。默认为 FLAT。 |
  |  15   | **state ** 设置按钮组件状态,可选的有NORMAL、ACTIVE、 DISABLED。默认 NORMAL。 |
  |  16   | **underline ** 下划线。默认按钮上的文本都不带下划线。取值就是带下划线的字符串索引，为 0 时，第一个字符带下划线，为 1 时，前两个字符带下划线，以此类推 |
  |  17   | **width ** 按钮的宽度，如未设置此项，其大小以适应按钮的内容（文本或图片的大小） |
  |  18   |         **wraplength ** 限制按钮每行显示的字符的数量         |
  |  19   |                   **text ** 按钮的文本内容                   |
  |  19   |        **anchor**  锚选项，控制文本的位置，默认为中心        |

