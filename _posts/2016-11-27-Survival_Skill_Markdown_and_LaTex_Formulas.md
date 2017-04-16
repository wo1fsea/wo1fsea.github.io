---
layout: post
title: "生存技能 01. Markdown & LaTex Formulas"
date: 2016-11-27 00:21:11 +0800
categories: Survival
keywords: Survival Skill, Markdown, LaTex
---

做笔记写文档最恼人的，可能就是文档标题格式字体字号等等的设置了。所幸用 Markdown 之后，都不用怎么考虑这些细节问题。然而，偶然读书遇到公式，想要记录下来还是挺麻烦的。最好的方法可能是截图，但是还是有格式不一致，不能二次编辑等等问题。这两天发现 atom 的 MPP 插件居然支持 LaTex 的公式编辑和渲染，再配合 google api 的 LaTex 公式渲染，正好解决了这个困扰已久的问题。

恰好最近正在反思，自己平时看书翻资料时间花不少，但记录总结的却不多。正好借机培养下习惯，从一些基础的东西开始整理。掌握这些工具，怎么看都是生存技能，所以这篇叫《生存技能 01. Markdown & LaTex Formulas》，从用 Markdown 和 LaTex 开始。

 <!-- more -->

 ## Markdown

> Markdown 是一种轻量级标记语言，创始人为约翰·格鲁伯（John Gruber）。它允许人们“使用易读易写的纯文本格式编写文档，然后转换成有效的XHTML(或者HTML)文档”。[4]这种语言吸收了很多在电子邮件中已有的纯文本标记的特性。 -- wikipedia

更具体的介绍可见 [Markdown官方主页](http://daringfireball.net/projects/markdown/) 。

下面列出 Markdown 语法的基本元素。

### 标题

Markdown 标题可以选用下列两种风格：

风格1:

    标题一
    ===

    标题二
    ---

或者，风格2，由1到6个#开头的标题形式：

    # 标题一

    ## 标题二

    ###### 标题六

### 引用

Markdown 引用使用 > 符号作为行起始字符，如下：

    > 引用

多行引用可写作：

    > 引用行1
    引用行2

或：

    > 引用行1
    > 引用行2

### 列表

无序列表写作：

    * a
    * b
    * c

或：

    + a
    + b
    + c

或：

    - a
    - b
    - c

有序列表：

    1. a
    2. b
    3. c

或：

    1. a
    1. b
    1. c

### 表格

在 Markdown 中使用如下格式：

    |h1  |h2  |h3  |
    |:---|:--:|---:|
    |v1  |v2  |v3  |

将生成下列表格：

|h1  |h2  |h3  |
|:---|:--:|---:|
|v1  |v2  |v3  |


### 代码块

Markdown 中缩进一个字表符或者4个空格表示一个代码块：

    这是文章。
        这是代码块。

或者使用

### 分割线

单行使用三个以上的下划线，星号，减号可以生成一个分割线。

    * * *

    ***

    *****

    - - -

    ---------------------------------------

### 图片

使用下列方式可插入图片：

    ![图片描述](图片地址)

### 链接

使用下列方式可插入链接：

    [链接描述](链接地址)

### 强调

在 Markdown 中，使用 `*斜体*` 表示 *斜体*，使用 `**粗体**` 表示 **粗体** 。


## LaTex 公式

### 上下标

在 LaTex 中，使用 ^ 表示上标， _ 表示下标。

例如：

    \[ E=mc^2 \]

表示

![](http://chart.googleapis.com/chart?cht=tx&chl=E=mc^2)


### 根式与分式

根式用\sqrt{·}来表示，分式用\frac{·}{·}来表示（第一个参数为分子，第二个为分母）。

例如：

    \[ \Large x=\frac{-b\pm\sqrt{b^2-4ac}}{2a} \]

表示：

![](http://chart.googleapis.com/chart?cht=tx&chl=\Large x=\frac{-b\pm\sqrt{b^2-4ac}}{2a})

### 运算符

下列运算符分别表示：

    \[ \pm\; \times \; \div\; \cdot\; \cap\; \cup\; \geq\; \leq\; \neq\; \approx\; \equiv\; \sum\; \prod\; \lim\; \int\; \]

![](http://chart.googleapis.com/chart?cht=tx&chl=\pm\; \times \; \div\; \cdot\; \cap\; \cup\; \geq\; \leq\; \neq\; \approx\; \equiv\; \sum\; \prod\; \lim\; \int\;)


### 分隔符

各种括号可使用 \big, \Big, \bigg, \Bigg 调整大小。

    \[ \Bigg[\bigg[\Big[\big[[x]\big]\Big]\bigg]\Bigg] \]
    \[ \Bigg\{\bigg\{\Big\{\big\{\{x\}\big\}\Big\}\bigg\}\Bigg\} \]
    \[ \Bigg\langle\bigg\langle\Big\langle\big\langle\langle{x}\rangle\big\rangle\Big\rangle\bigg\rangle\Bigg\rangle \]

![][a]
![][b]
![][c]

[a]:http://chart.googleapis.com/chart?cht=tx&chl=\Bigg(\bigg(\Big(\big((x)\big)\Big)\bigg)\Bigg)
[b]:http://chart.googleapis.com/chart?cht=tx&chl=\Bigg\{\bigg\{\Big\{\big\{\{x\}\big\}\Big\}\bigg\}\Bigg\}
[c]:http://chart.googleapis.com/chart?cht=tx&chl=\Bigg\langle\bigg\langle\Big\langle\big\langle\langle{x}\rangle\big\rangle\Big\rangle\bigg\rangle\Bigg\rangle

### 省略号

省略号有以下的表示：

    \[ x_1,x_2,\dots,x_n \quad 1,2,\cdots,n \quad \vdots \quad \ddots \]

![](http://chart.googleapis.com/chart?cht=tx&chl=x_1,x_2,\dots,x_n \quad 1,2,\cdots,n \quad \vdots \quad \ddots)

### 矩阵

有下列形式的矩阵描述：

    \[ \begin{pmatrix} a&b\\c&d \end{pmatrix} \quad
    \begin{bmatrix} a&b\\c&d \end{bmatrix} \quad
    \begin{Bmatrix} a&b\\c&d \end{Bmatrix} \quad
    \begin{vmatrix} a&b\\c&d \end{vmatrix} \quad
    \begin{Vmatrix} a&b\\c&d \end{Vmatrix} \]

![](http://chart.googleapis.com/chart?cht=tx&chl=\begin{pmatrix} a%26b\\c%26d \end{pmatrix})
![](http://chart.googleapis.com/chart?cht=tx&chl=\begin{bmatrix} a%26b\\c%26d \end{bmatrix})
![](http://chart.googleapis.com/chart?cht=tx&chl=\begin{Bmatrix} a%26b\\c%26d \end{Bmatrix})
![](http://chart.googleapis.com/chart?cht=tx&chl=\begin{vmatrix} a%26b\\c%26d \end{vmatrix})
![](http://chart.googleapis.com/chart?cht=tx&chl=\begin{Vmatrix} a%26b\\c%26d \end{Vmatrix})

### 分段函数

分段函数可以用 cases 来实现。

    \[ y=\begin{cases}
    -x,\quad x\leq 0 \\
    x,\quad x>0
    \end{cases} \]

![](http://chart.googleapis.com/chart?cht=tx&chl=y=\begin{cases} -x,\quad x\leq 0 \\x,\quad x>0\end{cases})

### 更多

详细的 LaTex 数学公式入门教程可参考 Michael Downes 的[Short Math Guide for LaTex](/files/LaTex/short_math_guide.pdf)。

## 将 LaTex 公式转换为图片

使用 google chart api 可以生成 LaTex 公式图片，接口如下：


    http://chart.googleapis.com/chart?cht=tx&chl=这里填写LaTex公式

例如：

    http://chart.googleapis.com/chart?cht=tx&chl=\Large x=\frac{-b\pm\sqrt{b^2-4ac}}{2a}

结果为：

![](http://chart.googleapis.com/chart?cht=tx&chl=\Large x=\frac{-b\pm\sqrt{b^2-4ac}}{2a})

需要注意的是在 url 中填入的公式中需要处理好 *&* 等字符的转换。
