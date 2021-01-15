---
layout:     post
title:      用PyInstaller打包.py文件
date:       2021-01-15
author:     "lhdaren"
header-img: "img/home-bg.jpg"
catalog: true
tags:
    - python
    - pyinstaller
    - PyQt
---

# 用PyInstaller打包.py文件
2021/1/4

## 背景
课程设计要用PyQt制作GUI并打包成.exe文件，在学习PyQt，就做了一个读取图片的小小程序，顺便试一下用PyInstaller打包。

## 正文

### PyInstaller的安装
我用的时候发现已经装好了，应该是我大三试着玩的时候装的，这里就不表了。

### PyInstaller的使用
1. `win + R`打开运行窗口，输入`cmd`后回车进入控制台
2. 进入要打包的py文件的目录
    - 输入`d:`进入D盘
    - 输入`cd D:\_study\Grade4-1\CourseDesign\learn_PyQt\mine`进入py文件所在文件夹
3. 输入指令`pyinstaller -F -w -i favicon.ico mine.py`
    - `-F` 生成的dist文件夹只含有单个的exe程序
    - `-w` exe文件打开时只有目标窗口，没有丑陋的控制台
    - `-i favicon.ico` 为程序加上指定路径的图标，这里只能是ico文件，jpg会报错我试了
    - `mine.py` 准备打包的py文件
4. 等待打包完成即可

### 打开exe文件
打包完成后文件夹里会新出现以下东西
- `_pycache_` `build` `dist` 三个文件夹
- `mine.spec`

exe文件即在dist文件夹中，点击运行即可

### 过程中遇到的问题及解决

#### 图标格式
- 必须是ico，用jpg在打包时会报错

#### 程序中要用到的图片素材等
1. 在代码引用素材时中使用相对路径`'res/faye.jpg'`
2. 打包结束后将`res`文件夹复制一份放在与exe文件并列的位置

#### 其他
- **似乎**需要在系统的主python环境中安装需要的库，我的PyQt一开始只装在anaconda创建的虚拟环境中，造成打包后点击exe不出现图形界面，在主环境中安装PyQt5后问题解决
> <font color=008080> 2021/1/15 更新： </font>
这个问题已经解决了，问题的根源在于 pyinstaller 在打包程序时会在主环境的 ``site-packages`` 文件夹中寻找需要的库。解决方法：在打包命令后加 ``-p 所需site-packages路径`` 如 ``-p D:\anaconda\envs\PyQt36\Lib\site-packages``

## 总结
这是我第一次写博文来记录学习，也是第一次使用markdown编辑文本，有些青涩见谅:)
另外感谢plb帮我测试在完全陌生环境下的程序运行。

## 附录

### [PyInstaller各参数含义](https://blog.csdn.net/weixin_39000819/article/details/80942423)

### 简易图形程序的代码

```python
import sys
import cv2
import numpy
from PyQt5.QtWidgets import QApplication, QWidget, QPushButton, QMessageBox, QLineEdit
from PyQt5.QtGui import QIcon
from random import randint

class Example(QWidget):

    def __init__(self):

        super().__init__()
        self.initUI()
        # self.filename = 'a.jpg'

    def initUI(self):

        self.setGeometry(300, 300, 300, 150)
        self.setWindowTitle('读取图片')
        self.setWindowIcon(QIcon('res/faye.jpg'))

        self.bt1 = QPushButton('读取', self)
        self.bt1.setGeometry(115, 90, 70 ,30)
        self.bt1.setToolTip('<b>点击这里读取图片</b>')
        self.bt1.clicked.connect(self.showMessage)  

        self.text = QLineEdit('在这里输入文件路径（不要含有中文）', self)
        self.text.selectAll()
        self.text.setFocus()
        self.text.setGeometry(25, 50, 250 ,30)

        self.show()

    def showMessage(self):

        filename = self.text.text()
        # print(filename)
        img = cv2.imread(filename)
        img1 = cv2.imread('a.jpg')
        
        if type(img) == type(img1):
            QMessageBox.about(self, '小问题...','路径好像错了')
            self.text.selectAll()
            self.text.setFocus()
        else:
            cv2.imshow(filename, img)
            cv2.waitKey(0)
            self.text.clear()
            self.text.setText('在这里输入文件路径（不要含有中文）')
            self.text.selectAll()
            self.text.setFocus()

    def closeEvent(self, event):

        reply = QMessageBox.question(self, '确认', '确认退出吗', QMessageBox.Yes | QMessageBox.No, QMessageBox.No)
        if reply == QMessageBox.Yes:
            event.accept()        
        else:
            event.ignore()  

if __name__ == '__main__':

    app = QApplication(sys.argv)
    ex = Example()
    sys.exit(app.exec_())
```