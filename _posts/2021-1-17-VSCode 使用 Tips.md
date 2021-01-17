---
layout:     post
title:      VSCode 使用 Tips
date:       2021-01-17
author:     "lhdaren"
header-img: "img/home-bg.jpg"
catalog: true
tags:
    - tips
    - VSCode
---


2021/1/9
> 本文记录一些使用VScode时的小Tips用于查阅

## 使用Tips
#### > [字体](https://zhuanlan.zhihu.com/p/65362086)
- ``ctrl + shift + p`` 打开 ``settings.json``
- 插入代码：
```
    "editor.fontFamily": "'Fira Code' ,'Source Han Sans CN'",//设置的字体类型(英文:Fira Code 中文:思源黑体)
    "editor.fontLigatures": true,//是否启用字体连字，true启用，false不启用
    "editor.fontSize": 19,//设置字体大小
    "editor.fontWeight": "normal",//设置字体粗细，可选normal,bold,"100"~"900"等
```



## 快捷键
#### > 注释

| 操作 | 快捷键 |
| :----: | :----: |
| 注释 |ctrl + k + c|
|取消注释|ctrl + k + u|
|多行注释 / 取消多行注释|alt + shift + a|

## 问题&解决

#### > PyTorch相关

[>> pylint 报错 Module torch has no xxx member](https://my.oschina.net/u/4176637/blog/4739698)
- ``Ctrl+Shift+P`` 打开vscode的命令面板，输入``settings.json`` 并打开第一项
- 在 ``settings.json`` 中插入以下代码
```
"python.linting.pylintArgs": [
        "--errors-only",
        "--generated-members=numpy.*, torch.*, cv2.*, cv.*"
    ]
```
- 保存

[>> pylint 报错 torch.tensor is not callable](https://www.pythonf.cn/read/155950)
- ``Ctrl+Shift+P``打开vscode的命令面板，输入``settings.json``并打开第一项
- 在``settings.json``中的``"python.linting.pylintArgs"``大括号里面插入以下代码
```
"--disable-msg=not-callable"
```
- 保存

[>> 错误:BrokenPipeError: [Errno 32] Broken pipe](https://blog.csdn.net/qq_33666011/article/details/81873217)
- 将 ``DataLoader`` 的 ``num_workers`` 参数值降低为0