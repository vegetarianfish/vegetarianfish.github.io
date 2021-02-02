---
layout: mypost
title: VSCode 使用 Tips
categories: [Tips, vscode]
---

2021/1/9
> 本文记录一些使用VScode时的小Tips用于查阅

* awsl
{:toc}

## 使用Tips
#### > [字体](https://zhuanlan.zhihu.com/p/65362086)
- ``ctrl + shift + p`` 打开 ``settings.json``
- 插入代码：
```cpp
    "editor.fontFamily": "'Fira Code' ,'Source Han Sans CN'",//设置的字体类型(英文:Fira Code 中文:思源黑体)
    "editor.fontLigatures": true,//是否启用字体连字，true启用，false不启用
    "editor.fontSize": 19,//设置字体大小
    "editor.fontWeight": "normal",//设置字体粗细，可选normal,bold,"100"~"900"等
```

#### > [实现文件夹右键用vscode打开](https://www.cnblogs.com/zmdblog/p/10202193.html)
1. 在 ``vscode.exe`` 所在目录下新建一 ``.txt`` 文件
2. 在 ``.txt`` 文件中粘贴如下代码（文件目录灵活选择）
3. 将文件后缀改为 ``.reg`` 后双击运行即可
<details>
<summary>点击展开代码</summary>
<pre><code>
Windows Registry Editor Version 5.00 

; Open files 
[HKEY_CLASSES_ROOT\*\shell\Open with VS Code] 
@="Edit with VS Code" 
"Icon"="C:\\Users\\hp\\AppData\\Local\\Programs\\Microsoft VS Code\\Code.exe,0" 

[HKEY_CLASSES_ROOT\*\shell\Open with VS Code\command] 
@="\"C:\\Users\\hp\\AppData\\Local\\Programs\\Microsoft VS Code\\Code.exe\" \"%1\"" 

; This will make it appear when you right click ON a folder 
; The "Icon" line can be removed if you don't want the icon to appear 

[HKEY_CLASSES_ROOT\Directory\shell\vscode] 
@="Open with VSCode" 
"Icon"="\"C:\\Users\\hp\\AppData\\Local\\Programs\\Microsoft VS Code\\Code.exe\",0" 

[HKEY_CLASSES_ROOT\Directory\shell\vscode\command] 
@="\"C:\\Users\\hp\\AppData\\Local\\Programs\\Microsoft VS Code\\Code.exe\" \"%1\"" 

; This will make it appear when you right click INSIDE a folder 
; The "Icon" line can be removed if you don't want the icon to appear 

[HKEY_CLASSES_ROOT\Directory\Background\shell\vscode] 
@="Open with VSCode" 
"Icon"="\"C:\\Users\\hp\\AppData\\Local\\Programs\\Microsoft VS Code\\Code.exe\",0" 

[HKEY_CLASSES_ROOT\Directory\Background\shell\vscode\command] 
@="\"C:\\Users\\hp\\AppData\\Local\\Programs\\Microsoft VS Code\\Code.exe\" \"%V\""
D:\\Microsoft VS Code\\Code.exe,0" 

[HKEY_CLASSES_ROOT\*\shell\Open with VS Code\command] 
@="\"C:\\Users\\hp\\AppData\\Local\\Programs\\Microsoft VS Code\\Code.exe\" \"%1\"" 

; This will make it appear when you right click ON a folder 
; The "Icon" line can be removed if you don't want the icon to appear 

[HKEY_CLASSES_ROOT\Directory\shell\vscode] 
@="Open with VSCode" 
"Icon"="\"C:\\Users\\hp\\AppData\\Local\\Programs\\Microsoft VS Code\\Code.exe\",0" 

[HKEY_CLASSES_ROOT\Directory\shell\vscode\command] 
@="\"C:\\Users\\hp\\AppData\\Local\\Programs\\Microsoft VS Code\\Code.exe\" \"%1\"" 

; This will make it appear when you right click INSIDE a folder 
; The "Icon" line can be removed if you don't want the icon to appear 

[HKEY_CLASSES_ROOT\Directory\Background\shell\vscode] 
@="Open with VSCode" 
"Icon"="\"C:\\Users\\hp\\AppData\\Local\\Programs\\Microsoft VS Code\\Code.exe\",0" 

[HKEY_CLASSES_ROOT\Directory\Background\shell\vscode\command] 
@="\"C:\\Users\\hp\\AppData\\Local\\Programs\\Microsoft VS Code\\Code.exe\" \"%V\""
</code></pre>
</details>

- 效果
![](https://i.loli.net/2021/01/28/sDIyFbZLa29RiWV.gif)

## 快捷键
| 操作 | 快捷键 |
| :----: | :----: |
| 注释 |ctrl + k + c|
|取消注释|ctrl + k + u|
|多行注释 / 取消多行注释|alt + shift + a|
|代码对齐|alt + shift + f|

## 问题&解决

#### > PyTorch相关

[>> pylint 报错 Module torch has no xxx member](https://my.oschina.net/u/4176637/blog/4739698)
- ``Ctrl+Shift+P`` 打开vscode的命令面板，输入``settings.json`` 并打开第一项
- 在 ``settings.json`` 中插入以下代码
```json
"python.linting.pylintArgs": [
        "--errors-only",
        "--generated-members=numpy.*, torch.*, cv2.*, cv.*"
    ]
```
- 保存

[>> pylint 报错 torch.tensor is not callable](https://www.pythonf.cn/read/155950)
- ``Ctrl+Shift+P``打开vscode的命令面板，输入``settings.json``并打开第一项
- 在``settings.json``中的``"python.linting.pylintArgs"``大括号里面插入以下代码
```json
"--disable-msg=not-callable"
```
- 保存

[>> 错误:BrokenPipeError: [Errno 32] Broken pipe](https://blog.csdn.net/qq_33666011/article/details/81873217)

- 将 ``DataLoader`` 的 ``num_workers`` 参数值降低为0