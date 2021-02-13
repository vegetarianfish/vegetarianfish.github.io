---
layout: mypost
title: Markdown 使用 Tips
categories: [Tips, markdown]
---

> 更详细的语法看这里[这里](https://www.zybuluo.com/mdeditor)

## 添加目录
1. 博客 & Typora

```
* awsl
{:toc}
```
* awsl
{:toc}

2. VSCode & Typora

```
[TOC]
```
[TOC]

## 文字颜色
```
<font color=008080> 要改变颜色的文字 </font>

<font color='red'> 要改变颜色的文字 </font>
```

<font color=008080> 要改变颜色的文字 </font>

<font color='red'> 要改变颜色的文字 </font>

## 内容折叠

```
<details>
<summary>此处为被折叠内容的标题</summary>
此处为被折叠的内容
</details>

<details>
<summary>展开查看代码</summary>
<pre><code>
import sys
import cv2
import numpy
from PyQt5.QtWidgets import QFileDialog, QApplication, QWidget, QPushButton, QMessageBox, QLineEdit
from PyQt5.QtGui import QIcon
from random import randint
</code></pre>
</details>
```

<details>
<summary>此处为被折叠内容的标题</summary>
此处为被折叠的内容
</details>

<details>
<summary>展开查看代码</summary>
<pre><code>
import sys
import cv2
import numpy
from PyQt5.QtWidgets import QFileDialog, QApplication, QWidget, QPushButton, QMessageBox, QLineEdit
from PyQt5.QtGui import QIcon
from random import randint
</code></pre>
</details>

## [页内跳转](https://www.jianshu.com/p/0c5d6678ed6d)

```
[点此跳转](#jump)
#
#
#
#
#
#
#
#
#
#
<span id="jump">Hello World</span>
```

[点此跳转](#jump)
#
#
#
#
#
#
#
#
#
#
<span id="jump">Hello World</span>