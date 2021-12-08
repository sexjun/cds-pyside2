@[toc]


---
- 安装pyside2
`pip install pyside2 -i https://pypi.douban.com/simple/`

---

## 界面设计

界面设计我们可以用QT界面生成器 `Qt Designer` ，使用方法有二种：
1. 动态加载UI文件
	```
	self.ui = QUiLoader().load('untitled.ui')
	```
2. 转化UI文件为Python代码
	```
	pyside2-uic main.ui > ui_main.py
	```
Windows下，运行 Python安装目录下 Scripts\pyside2-designer.exe 这个可执行文件

- 那么我们该使用哪种方式比较好呢？动态加载还是转化为Python代码？
通常采用动态加载比较方便，因为改动界面后，不需要转化，直接运行，特别方便。
但是，如果 你的程序里面有非qt designer提供的控件， 这时候，需要在代码里面加上一些额外的声明，而且 可能还会有奇怪的问题。往往就 要采用 转化Python代码的方法。

- 我的UI编辑路径是：
C:\Users\cds\.conda\envs\pyside2_qt\Scripts
![在这里插入图片描述](https://img-blog.csdnimg.cn/727071e1c3254658899996ff6b27f791.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5pep552h55qE5Y-25a2Q,size_20,color_FFFFFF,t_70,g_se,x_16)


## 界面布局
有四种常用的布局方式
1. QHBoxLayout 水平布局
![在这里插入图片描述](https://img-blog.csdnimg.cn/5b161e26a45441b6b4d0809898743b7a.png)
2. QVBoxLayout 垂直布局
![在这里插入图片描述](https://img-blog.csdnimg.cn/b2ba51fe06684816a72106612f552080.png)
3. QGridLayout 表格布局
![在这里插入图片描述](https://img-blog.csdnimg.cn/dd6ea5affa174e818b4774bb1e4e2c40.png)
4. QFormLayout 表单布局
![在这里插入图片描述](https://img-blog.csdnimg.cn/cc22cb96e4ac4e719cdcfa9b68126e95.png)

## 常用控件
- [官方控件学习文档](https://doc.qt.io/archives/qtforpython-5.12/PySide2/QtWidgets/index.html#module-PySide2.QtWidgets)
- [国内好的博客](https://www.byhy.net/tut/py/gui/qt_05_1/)

## 从一个窗口跳转到另外一个窗口
如果经常要在两个窗口来回跳转，可以使用 hide() 方法 隐藏窗口， 而不是 closes() 方法关闭窗口。 这样还有一个好处：被隐藏的窗口再次显示时，原来的操作内容还保存着，不会消失。



## 发布程序
### 1. 打包
我们可以使用 PyInstaller 来制作独立可执行程序。

```

pyinstaller httpclient.py --noconsole --hidden-import PySide2.QtXml
```

- `--noconsole `指定不要命令行窗口，否则我们的程序运行的时候，还会多一个黑窗口。 但是我建议大家可以先去掉这个参数，等确定运行成功后，再加上参数重新制作exe。因为这个黑窗口可以显示出程序的报错，这样我们容易找到问题的线索。
- `--hidden-import PySide2.QtXml `参数是因为这个 QtXml库是动态导入，PyInstaller没法分析出来，需要我们告诉它，

最后，别忘了，把程序所需要的ui文件拷贝到打包目录中。

因为PyInstaller只能分析出需要哪些代码文件。 而你的程序动态打开的资源文件，比如 图片、excel、ui这些，它是不会帮你打包的。

我们的 示例代码 需要 从 httpclient.ui 中加载界面，手动拷贝到 dist/httpclient 目录中。

然后，再双击运行 httpclient.exe ，完美!

###  2. 程序图标
通过如下代码，我们可以把一个png图片文件作为 程序窗口图标。
```python

from PySide2.QtGui import  QIcon

app = QApplication([])
# 加载 icon
app.setWindowIcon(QIcon('logo.png'))
```
注意：这些图标png文件，在使用PyInstaller创建可执行程序时，也要拷贝到程序所在目录。否则可执行程序运行后不会显示图标。

### 3.应用程序图标
应用程序图标是放在可执行程序里面的资源。

可以在PyInstaller创建可执行程序时，通过参数 --icon="logo.ico" 指定。

比如
```
pyinstaller httpclient.py --noconsole --hidden-import PySide2.QtXml --icon="logo.ico"
```
注意参数一定是存在的ico文件，不能是png等图片文件。

如果你只有png文件，可以通过在线的png转ico文件网站，生成ico，比如下面两个网站
- [https://www.zamzar.com/convert/png-to-ico/](https://www.zamzar.com/convert/png-to-ico/)
- [https://www.easyicon.net/covert/](https://www.zamzar.com/convert/png-to-ico/)
注意：这些应用程序图标ico文件，在使用PyInstaller创建可执行程序时，不需要要拷贝到程序所在目录。因为它已经被嵌入可执行程序了。



## 简单封装一个界面的demo

![在这里插入图片描述](https://img-blog.csdnimg.cn/97f6f9902fe94f07b4f849262c3e83f3.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5pep552h55qE5Y-25a2Q,size_15,color_FFFFFF,t_70,g_se,x_16)

```

from PySide2.QtWidgets import QApplication, QMainWindow, QPushButton,  QPlainTextEdit,QMessageBox

class Stats():
    def __init__(self):
        self.window = QMainWindow()
        self.window.resize(500, 400)
        self.window.move(300, 300)
        self.window.setWindowTitle('薪资统计')

        self.textEdit = QPlainTextEdit(self.window)
        self.textEdit.setPlaceholderText("请输入薪资表")
        self.textEdit.move(10, 25)
        self.textEdit.resize(300, 350)

        self.button = QPushButton('统计', self.window)
        self.button.move(380, 80)

        self.button.clicked.connect(self.handleCalc)


    def handleCalc(self):
        info = self.textEdit.toPlainText()

        # 薪资20000 以上 和 以下 的人员名单
        salary_above_20k = ''
        salary_below_20k = ''
        for line in info.splitlines():
            if not line.strip():
                continue
            parts = line.split(' ')
            # 去掉列表中的空字符串内容
            parts = [p for p in parts if p]
            name,salary,age = parts
            if int(salary) >= 20000:
                salary_above_20k += name + '\n'
            else:
                salary_below_20k += name + '\n'

        QMessageBox.about(self.window,
                    '统计结果',
                    f'''薪资20000 以上的有：\n{salary_above_20k}
                    \n薪资20000 以下的有：\n{salary_below_20k}'''
                    )

app = QApplication([])
stats = Stats()
stats.window.show()
app.exec_()
```