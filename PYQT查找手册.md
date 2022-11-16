



## Button

```python
# 设置按钮状态
button.setCheckable(True)
# 返回按钮状态
button.isChecked()
# 设置按钮是否可按
button.setEnabled(False)
button.setDisabled(True)

```

```python
# ！槽信号

# 按钮 点击  
self.button.clicked.connect(self.the_button_was_clicked)
# 窗口 标题改变 
self.windowTitleChanged.connect(self.the_window_title_changed)
```

---

## Mouse

```python
'''
直接申明在窗口类中，无需槽函数，直接触发鼠标事件
'''
# 按住 移动
def mouseMoveEvent(self, e):
    self.label.setText("mouseMoveEvent")
# 按住
def mousePressEvent(self, e):
    self.label.setText("mousePressEvent")
# 松开按键
def mouseReleaseEvent(self, e):
    self.label.setText("mouseReleaseEvent")
# 双击
def mouseDoubleClickEvent(self, e):
    self.label.setText("mouseDoubleClickEvent")
```

| 方法           | 返回                                 |
| -------------- | ------------------------------------ |
| `.button()`    | 触发此事件的特定按钮                 |
| `.buttons()`   | 所有鼠标按钮的状态（或已标记的旗帜） |
| `.globalPos()` | 应用-全球地位`QPoint`                |
| `.globalX()`   | 应用-全球*水平*X位置                 |
| `.globalY()`   | 应用-全球*垂直*Y位置                 |
| `.pos()`       | 小部件相对位置作为*整数*`QPoint`     |
| `.posF()`      | 小部件相对位置作为*浮子*`QPointF`    |



```python
  # 区分 鼠标 左右中 三键反应
    def mousePressEvent(self, e):
        if e.button() == Qt.LeftButton:
            # handle the left-button press in here
            self.label.setText("mousePressEvent LEFT")

        elif e.button() == Qt.MiddleButton:
            # handle the middle-button press in here.
            self.label.setText("mousePressEvent MIDDLE")

        elif e.button() == Qt.RightButton:
            # handle the right-button press in here.
            self.label.setText("mousePressEvent RIGHT")
```

| 标识符            | 值（二进制） | 代表                                   |
| ----------------- | ------------ | -------------------------------------- |
| `Qt.NoButton`     | 3 k`000`)    | 没有按下按钮，或者事件与按下按钮无关。 |
| `Qt.LeftButton`   | 2 k`001`)    | 按下左键                               |
| `Qt.RightButton`  | 1 k`010`)    | 按下正确的按钮。                       |
| `Qt.MiddleButton` | 4 (`100`)    | 按下中间按钮。                         |

---

## Context Menu 右键小菜单

```python
  # 无需槽函数，在类内直接触发
  def contextMenuEvent(self, e):
        context = QMenu(self)
        context.addAction(QAction("test 1", self))
        context.addAction(QAction("test 2", self))
        context.addAction(QAction("test 3", self))
        context.exec(e.globalPos())
```

```python
# 槽函数实现
class MainWindow(QMainWindow):
    def __init__(self):
        super().__init__()
        self.show()

        self.setContextMenuPolicy(Qt.CustomContextMenu)
        self.customContextMenuRequested.connect(self.on_context_menu)

    def on_context_menu(self, pos):
        context = QMenu(self)
        context.addAction(QAction("test 1", self))
        context.addAction(QAction("test 2", self))
        context.addAction(QAction("test 3", self))
        context.exec(self.mapToGlobal(pos))
```



![image-20211103204004006](C:/Users/Meroke/AppData/Roaming/Typora/typora-user-images/image-20211103204004006.png)



---

## Widget

| 控件             | 它的作用                 |
| ---------------- | ------------------------ |
| `QCheckbox`      | 复选框                   |
| `QComboBox`      | 下拉列表框               |
| `QDateEdit`      | 用于编辑日期和日期       |
| `QDateTimeEdit`  | 用于编辑日期和日期       |
| `QDial`          | 可旋转表盘               |
| `QDoubleSpinbox` | 浮动的数字微调器         |
| `QFontComboBox`  | 字体列表                 |
| `QLCDNumber`     | 相当丑陋的液晶显示屏     |
| `QLabel`         | 只是一个标签，不是互动的 |
| `QLineEdit`      | 输入文本行               |
| `QProgressBar`   | 进度栏                   |
| `QPushButton`    | 按钮                     |
| `QRadioButton`   | 切换集，只有一个活动项目 |
| `QSlider`        | 滑块                     |
| `QSpinBox`       | 整数微调器               |
| `QTimeEdit`      | 用于编辑时间             |



### Pix 图片

```python
# 放置图片
widget.setPixmap(QPixmap('otje.jpg'))
# 自由伸缩，图片完全适应屏幕
widget.setScaledContents(True)
```



### Label

```python
		widget = QLabel("Hello")
        font = widget.font()
        font.setPointSize(30)
        widget.setFont(font)
        widget.setAlignment(Qt.AlignHCenter | Qt.AlignVCenter)
```

可用于水平对齐的旗帜是：`Qt.`

| PyQt5 旗          | 行为                     |
| ----------------- | ------------------------ |
| `Qt.AlignLeft`    | 与左边缘对齐。           |
| `Qt.AlignRight`   | 与右边缘对齐。           |
| `Qt.AlignHCenter` | 在可用空间中水平中心。   |
| `Qt.AlignJustify` | 在可用空间中为文本辩护。 |

可用于垂直对齐的旗帜是：

| PyQt5 旗          | 行为                   |
| ----------------- | ---------------------- |
| `Qt.AlignTop`     | 与顶部对齐。           |
| `Qt.AlignBottom`  | 与底部对齐。           |
| `Qt.AlignVCenter` | 在可用空间中垂直中心。 |



### Check Box

```python
        widget = QCheckBox()
        widget.setCheckState(Qt.Checked)

        # For tristate: widget.setCheckState(Qt.PartiallyChecked)
        # Or: widget.setTriState(True)
        widget.stateChanged.connect(self.show_state)
 def show_state(self, s):
    print(s == Qt.Checked)
    print(s)
```

![窗口、Mac 和乌本图利努的 Qcheckbox 。](https://www.pythonguis.com/tutorials/pyside6-widgets/widgets3.png)

| PyQt5 旗              | 行为           |
| --------------------- | -------------- |
| `Qt.Unchecked`        | 项目未检查     |
| `Qt.PartiallyChecked` | 项目已部分检查 |
| `Qt.Checked`          | 项目未检查     |



### ComboBox 下拉列表

```python
    widget = QComboBox()
    widget.addItems(["One", "Two", "Three"])

    # Sends the current index (position) of the selected item.
    widget.currentIndexChanged.connect(self.index_changed)

    # There is an alternate signal to send the text.
    widget.currentTextChanged.connect(self.text_changed)

    self.setCentralWidget(widget)

# i 为填入内容在列表中的序号
def index_changed(self, i):  # i is an int
    print(i)
# str 为填入内容
def text_changed(self, s):  # s is a str
    print(s)
```

```python
# 允许用户自由输入
widget.setEditable(True)
```

您还可以设置一个标志来确定如何处理插入。这些标志存储在类本身上，并列在下面：sadasdaswq

| PyQt5 旗                         | 行为               |
| -------------------------------- | ------------------ |
| `QComboBox.NoInsert`             | 无插入             |
| `QComboBox.InsertAtTop`          | 作为第一个项目插入 |
| `QComboBox.InsertAtCurrent`      | 替换当前选定的项目 |
| `QComboBox.InsertAtBottom`       | 最后一个项目后插入 |
| `QComboBox.InsertAfterCurrent`   | 当前项目后插入     |
| `QComboBox.InsertBeforeCurrent`  | 在当前项目之前插入 |
| `QComboBox.InsertAlphabetically` | 按字母顺序插入     |

### listwidget 滚动列表

```python
 widget = QListWidget()
    widget.addItems(["One", "Two", "Three"])

    widget.currentItemChanged.connect(self.index_changed)
    widget.currentTextChanged.connect(self.text_changed)

    self.setCentralWidget(widget)

# 类似combox，但是index仍是内容 而非下标
def index_changed(self, i): # Not an index, i is a QListItem
    print(i.text())

def text_changed(self, s): # s is a str
    print(s)
```

![QListWidget on Windows, Mac & Ubuntu Linux.](https://www.pythonguis.com/tutorials/pyside6-widgets/widgets5.png)

### lineEdit

```
    widget = QLineEdit()
    widget.setMaxLength(10)
    widget.setPlaceholderText("Enter your text")

    #widget.setReadOnly(True) # uncomment this to make readonly

    widget.returnPressed.connect(self.return_pressed)
    widget.selectionChanged.connect(self.selection_changed)
    widget.textChanged.connect(self.text_changed)
    widget.textEdited.connect(self.text_edited)
    widget.setInputMask('000.000.000.000;_')
    # 增加了输入格式，如下为 IPV4格式，验证ip地址
    self.setCentralWidget(widget)


def return_pressed(self):
    print("Return pressed!")
    self.centralWidget().setText("BOOM!")

def selection_changed(self):
    print("Selection changed")
    print(self.centralWidget().selectedText())

def text_changed(self, s):
    print("Text changed...")
    print(s)

def text_edited(self, s):
    print("Text edited...")
    print(s)
```



### QspinBox

```python
widget = QSpinBox()
# Or: widget = QDoubleSpinBox()

widget.setMinimum(-10)
widget.setMaximum(3)
# Or: widget.setRange(-10,3)

widget.setPrefix("$")
widget.setSuffix("c")
widget.setSingleStep(3)  # Or e.g. 0.5 for QDoubleSpinBox
widget.valueChanged.connect(self.value_changed)
widget.textChanged.connect(self.value_changed_str)
```

![窗口、Mac 和乌本图利努的 Qspinbox 。](https://www.pythonguis.com/tutorials/pyside6-widgets/widgets7.png)

### QSlieder

```python
widget = QSlider()

widget.setMinimum(-10)
widget.setMaximum(3)
# Or: widget.setRange(-10,3)

widget.setSingleStep(3)

widget.valueChanged.connect(self.value_changed)
widget.sliderMoved.connect(self.slider_position)
widget.sliderPressed.connect(self.slider_pressed)
widget.sliderReleased.connect(self.slider_released)
```

您也可以在创建方向时通过方向来构建具有垂直或水平方向的滑块。方向标志在命名空间中定义。例如 

```
widget.QSlider(Qt.Vertical)
```

```
widget.QSlider(Qt.Horizontal)
```

![视窗上的 Q 滑翔机， Mac 和乌本图利努。](https://www.pythonguis.com/tutorials/pyside6-widgets/widgets8.png)

### QDial

```python
		widget = QDial()
        widget.setRange(-10, 100)
        widget.setSingleStep(0.5)

        widget.valueChanged.connect(self.value_changed)
        widget.sliderMoved.connect(self.slider_position)
        widget.sliderPressed.connect(self.slider_pressed)
        widget.sliderReleased.connect(self.slider_released)
```

![窗口、Mac 和乌本图利努的 Qdial 。](https://www.pythonguis.com/tutorials/pyside6-widgets/widgets9.png)



---

## Layout Manager

### *Layout

```python
class Color(QWidget):

    def __init__(self, color):
        super(Color, self).__init__()
        self.setAutoFillBackground(True)

        palette = self.palette()
        palette.setColor(QPalette.Window, QColor(color))
        self.setPalette(palette)
        
        
class MainWindow(QMainWindow):
    def __init__(self):
        super(MainWindow, self).__init__()
        self.setWindowTitle("My App")
		
        # QV 和 QH 分别是垂直分布，水平分布
        # layout = QVBoxLayout()
        layout = QHBoxLayout()
        layout.addWidget(Color('red'))
        layout.addWidget(Color('green'))
        layout.addWidget(Color('blue'))

        widget = QWidget()
        widget.setLayout(layout)
        self.setCentralWidget(widget)        
```

```python
# 嵌套效果
layout1 = QHBoxLayout()
layout2 = QVBoxLayout()
layout3 = QVBoxLayout()

layout2.addWidget(Color('red'))
layout2.addWidget(Color('yellow'))
layout2.addWidget(Color('purple'))

layout1.addLayout( layout2 )

layout1.addWidget(Color('green'))

layout3.addWidget(Color('red'))
layout3.addWidget(Color('purple'))

layout1.addLayout( layout3 )

widget = QWidget()
widget.setLayout(layout1)
self.setCentralWidget(widget)
```

![image-20211103222915924](C:/Users/Meroke/AppData/Roaming/Typora/typora-user-images/image-20211103222915924.png)



### Grid

```python
layout = QGridLayout()

layout.addWidget(Color('red'), 0, 0)
layout.addWidget(Color('green'), 1, 0)
layout.addWidget(Color('blue'), 1, 1)
layout.addWidget(Color('purple'), 2, 1)

widget = QWidget()
widget.setLayout(layout)
self.setCentralWidget(widget)
```

![带未填充插槽的 Q 格里德拉布。](https://www.pythonguis.com/tutorials/pyside-layouts/gridlayout2.png)

### *Stack

可用于切换页面

```python
layout = QStackedLayout()

layout.addWidget(Color("red"))
layout.addWidget(Color("green"))
layout.addWidget(Color("blue"))
layout.addWidget(Color("yellow"))

layout.setCurrentIndex(2)

widget = QWidget()
widget.setLayout(layout)
self.setCentralWidget(widget)
```

```python
# 页面切换核心
	pagelayout = QVBoxLayout()
    button_layout = QHBoxLayout()
    self.stacklayout = QStackedLayout()

    pagelayout.addLayout(button_layout)
    pagelayout.addLayout(self.stacklayout)

    btn = QPushButton("red")
    btn.pressed.connect(self.activate_tab_1)
    button_layout.addWidget(btn)
    self.stacklayout.addWidget(Color("red"))

    btn = QPushButton("green")
    btn.pressed.connect(self.activate_tab_2)
    button_layout.addWidget(btn)
    self.stacklayout.addWidget(Color("green"))

    btn = QPushButton("yellow")
    btn.pressed.connect(self.activate_tab_3)
    button_layout.addWidget(btn)
    self.stacklayout.addWidget(Color("yellow"))

    widget = QWidget()
    widget.setLayout(pagelayout)
    self.setCentralWidget(widget)

def activate_tab_1(self):
    self.stacklayout.setCurrentIndex(0)

def activate_tab_2(self):
    self.stacklayout.setCurrentIndex(1)

def activate_tab_3(self):
    self.stacklayout.setCurrentIndex(2)
```

![A custom tab-like interface implemented using QStackedLayout.](https://www.pythonguis.com/tutorials/pyside-layouts/layout8.png)

### Tab Widget

功能同Stack，页面切换

```python
import sys

from PyQt5.QtCore import Qt
from PyQt5.QtWidgets import (
    QApplication,
    QLabel,
    QMainWindow,
    QPushButton,
    QTabWidget,
    QWidget,
)

from PyQt5.QtGui import QPalette, QColor
class Color(QWidget):

    def __init__(self, color):
        super(Color, self).__init__()
        self.setAutoFillBackground(True)

        palette = self.palette()
        palette.setColor(QPalette.Window, QColor(color))
        self.setPalette(palette)

class MainWindow(QMainWindow):
    def __init__(self):
        super().__init__()

        self.setWindowTitle("My App")

        tabs = QTabWidget()
        tabs.setTabPosition(QTabWidget.West)
        tabs.setMovable(True)

        for n, color in enumerate(["red", "green", "blue", "yellow"]):
            tabs.addTab(Color(color), color)

        self.setCentralWidget(tabs)


app = QApplication(sys.argv)

window = MainWindow()
window.show()

app.exec()
```

![QTabWidget in document mode on macOS.](https://www.pythonguis.com/tutorials/pyside-layouts/layout9-mac-document.png)

---

## TollBar



```python
        # 新建工具箱
    	toolbar = QToolBar("My main toolbar")
        self.addToolBar(toolbar)
		# 新建触发动作
        button_action = QAction("Your button", self)
        button_action.setStatusTip("This is your button")
        button_action.triggered.connect(self.onMyToolBarButtonClick)
        # 工具栏绑定触发动作
        toolbar.addAction(button_action)
 	# 槽函数
    def onMyToolBarButtonClick(self, state):
        print("click", state)
```

请注意，Qt 使用操作系统默认设置来确定是显示工具栏中的图标、文本还是图标和文本。但是，你可以使用覆盖这一点。此插槽接受名称空间中的以下任何标志：.setToolButtonStyle()

| PyQt5 旗                      | 行为                       |
| ----------------------------- | -------------------------- |
| `Qt.ToolButtonIconOnly`       | 仅限图标，无文本           |
| `Qt.ToolButtonTextOnly`       | 仅限文本，无图标           |
| `Qt.ToolButtonTextBesideIcon` | 图标和文本，图标旁边有文本 |
| `Qt.ToolButtonTextUnderIcon`  | 图标和文本，图标下有文本   |
| `Qt.ToolButtonFollowStyle`    | 遵循主机桌面风格           |

```python
		toolbar = QToolBar("My main toolbar")
        toolbar.setIconSize(QSize(16, 16))
        self.addToolBar(toolbar)

        button_action = QAction(QIcon("E:\\Program\\Code\\python\\Python_pra\\PYQT\\study\\ToolBar\\alpha.png"), "&Your button", self)
        button_action.setStatusTip("This is your button")
        button_action.triggered.connect(self.onMyToolBarButtonClick)
        # 按钮可检测状态，按下，非按下，有动画显示
        button_action.setCheckable(True)
        toolbar.addAction(button_action)
		# 添加一个分隔栏，分开工具栏中的按钮
        toolbar.addSeparator()

        button_action2 = QAction(QIcon("bug.png"), "Your &button2", self)
        button_action2.setStatusTip("This is your button2")
        button_action2.triggered.connect(self.onMyToolBarButtonClick)
        button_action2.setCheckable(True)
        toolbar.addAction(button_action2)

        toolbar.addWidget(QLabel("Hello"))
        # 添加了一个复选框
        toolbar.addWidget(QCheckBox())
		# 添加状态栏，可见
        self.setStatusBar(QStatusBar(self))

    def onMyToolBarButtonClick(self, s):
        print("click", s)
```



```python
	# 菜单界面 类似工具栏，操作一致
        button_action =    		QAction(QIcon("E:\\Program\\Code\\python\\Python_pra\\PYQT\\study\\ToolBar\\alpha.png"), "&Your button", self)
        button_action.setStatusTip("This is your button")
        button_action.triggered.connect(self.onMyToolBarButtonClick)
        button_action.setCheckable(True)
        
        menu = self.menuBar()
        file_menu = menu.addMenu("&File")
        file_menu.addAction(button_action)
```

![窗口上显示的菜单 - 在 macOS 上，这将位于屏幕顶部。](https://www.pythonguis.com/tutorials/pyside6-actions-toolbars-menus/actions7.png)

```python
        
    	file_menu = menu.addMenu("&File")
        file_menu.addAction(button_action)
        # 添加分隔栏
        file_menu.addSeparator()
        file_menu.addAction(button_action2)
```

![我们的操作显示在菜单中。](https://www.pythonguis.com/tutorials/pyside6-actions-toolbars-menus/actions8.png)

```python
        file_menu = menu.addMenu("&File")
        file_menu.addAction(button_action)
        file_menu.addSeparator()
        file_menu.addAction(button_action2)
		# 添加子菜单
        file_submenu = file_menu.addMenu("Submenu")
        file_submenu.addAction(button_action2)
```

![苏梅努嵌套在文件菜单中。](https://www.pythonguis.com/tutorials/pyside6-actions-toolbars-menus/actions9.png)

```python
        button_action = QAction(QIcon("E:\\Program\\Code\\python\\Python_pra\\PYQT\\study\\ToolBar\\alpha.png"), "&Your button", self)
        button_action.setStatusTip("This is your button")
        button_action.triggered.connect(self.onMyToolBarButtonClick)
        button_action.setCheckable(True)
        # 添加快捷键
        button_action.setShortcut(QKeySequence("Ctrl+p"))
        toolbar.addAction(button_action)
```



### Dialog

```python
# 作为一个继承QDialog的子类，通过添加内部件 ，拓展其性能
class CustomDialog(QDialog):
    def __init__(self):
        super().__init__()
		# 添加了窗口标题
        self.setWindowTitle("HELLO!")
		# 新建按钮，ok 和 cancel
        QBtn = QDialogButtonBox.Ok | QDialogButtonBox.Cancel
		#  绑定部件
        self.buttonBox = QDialogButtonBox(QBtn)
		# 添加 点击后的功能
        self.buttonBox.accepted.connect(self.accept)
        self.buttonBox.rejected.connect(self.reject)
		# 垂直布局
        self.layout = QVBoxLayout()
        message = QLabel("Something happened, is that OK?")
        # 布局 添加 Label组件
        self.layout.addWidget(message)
        # 布局 添加 buttonBox组件
        self.layout.addWidget(self.buttonBox)
        # 使用布局
        self.setLayout(self.layout)

```

```python
		# 使用实例
		dlg = CustomDialog()
         dlg.exec()
```

- `QDialogButtonBox.Ok`
- `QDialogButtonBox.Open`
- `QDialogButtonBox.Save`
- `QDialogButtonBox.Cancel`
- `QDialogButtonBox.Close`
- `QDialogButtonBox.Discard`
- `QDialogButtonBox.Apply`
- `QDialogButtonBox.Reset`
- `QDialogButtonBox.RestoreDefaults`
- `QDialogButtonBox.Help`
- `QDialogButtonBox.SaveAll`
- `QDialogButtonBox.Yes`
- `QDialogButtonBox.YesToAll`
- `QDialogButtonBox.No`
- `QDialogButtonBox.Abort`
- `QDialogButtonBox.Retry`
- `QDialogButtonBox.Ignore`
- `QDialogButtonBox.NoButton`



### MessageBox

```python
		button = QPushButton("Press me for a dialog!")
        button.clicked.connect(self.button_clicked)
        self.setCentralWidget(button)

    def button_clicked(self, s):
        dlg = QMessageBox(self)
        dlg.setWindowTitle("I have a question!")
        dlg.setText("This is a simple dialog")
        # 获取MessageBox 的返回值
        button = dlg.exec()

        if button == QMessageBox.Ok:
            print("OK!")
```

- `QMessageBox.Ok`
- `QMessageBox.Open`
- `QMessageBox.Save`
- `QMessageBox.Cancel`
- `QMessageBox.Close`
- `QMessageBox.Discard`
- `QMessageBox.Apply`
- `QMessageBox.Reset`
- `QMessageBox.RestoreDefaults`
- `QMessageBox.Help`
- `QMessageBox.SaveAll`
- `QMessageBox.Yes`
- `QMessageBox.YesToAll`
- `QMessageBox.No`
- `QMessageBox.NoToAll`
- `QMessageBox.Abort`
- `QMessageBox.Retry`
- `QMessageBox.Ignore`
- `QMessageBox.NoButton`

| 图标状态                  | 描述                     |
| ------------------------- | ------------------------ |
| `QMessageBox.NoIcon`      | 留言箱没有图标。         |
| `QMessageBox.Question`    | 消息是问一个问题。       |
| `QMessageBox.Information` | 该消息仅是信息。         |
| `QMessageBox.Warning`     | 消息是警告。             |
| `QMessageBox.Critical`    | 该消息表示存在关键问题。 |



```python
    def button_clicked(self, s):
        dlg = QMessageBox(self)
        dlg.setWindowTitle("I have a question!")
        dlg.setText("This is a question dialog")
        dlg.setStandardButtons(QMessageBox.Yes | QMessageBox.No)
        dlg.setIcon(QMessageBox.Question)
        button = dlg.exec()

        if button == QMessageBox.Yes:
            print("Yes!")
        else:
            print("No!")
```

