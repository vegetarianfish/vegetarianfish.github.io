---
layout: post
title:  "A Test Blog"
date:   2021-01-14 17:06:00 +0800
categories: jekyll update
---
# PyQt实现文件浏览
2021/1/5
## 背景
课程设计GUI需要，补充在[之前文章](D:/Blogs&Notes/2021.01/1.用PyInstaller打包.py文件.md)的简易图片读取软件上
## 实现
首先要定义一个常规按钮
```python
self.bt2 = QPushButton('浏览', self)
self.bt2.setGeometry(80, 90, 70 ,30) 
self.bt2.setToolTip('<b>点击这里浏览文件</b>')
```
将按钮链接到文件浏览功能中
```python
self.bt2.clicked.connect(self.setBrowerPath)
```
文件浏览模块
```python
def setBrowerPath(self): 
    
    file_path, file_type = QFileDialog.getOpenFileName(self, '打开文件','./',("Images (*.png *.xpm *.jpg)"))
    self.text.setText(file_path)
    self.text.selectAll()
    self.text.setFocus()
    # 上面"Images (*.png *.xpm *.jpg)"的意义是只能选择后缀在指定范围内的文件
    # 如果要选择文件目录而不是具体文件，使用：
    # directory_path = QFileDialog.getExistingDirectory(self, '打开文件夹','./')
```
## 效果
![](https://i.loli.net/2021/01/05/U3wVNYuSqr45aKI.gif)
## 附录
<details>
<summary>完整代码</summary>

```python
import sys
import cv2
import numpy
from PyQt5.QtWidgets import QFileDialog, QApplication, QWidget, QPushButton, QMessageBox, QLineEdit
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
        self.bt1.setGeometry(150, 90, 70 ,30)
        self.bt1.setToolTip('<b>点击这里读取图片</b>')
        self.bt1.clicked.connect(self.showMessage)

        self.bt2 = QPushButton('浏览', self)
        self.bt2.setGeometry(80, 90, 70 ,30)
        self.bt2.setToolTip('<b>点击这里浏览文件</b>')
        self.bt2.clicked.connect(self.setBrowerPath)

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

    def setBrowerPath(self): 
        
        file_path, file_type = QFileDialog.getOpenFileName(self, '打开文件','./',("Images (*.png *.xpm *.jpg)"))   
        self.text.setText(file_path)
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
</details>