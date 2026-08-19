# Houdini 自定义 Python Panel 面板教学

> 适用版本：Houdini 21.x（使用 PySide6）
> 本文档将从零开始，讲解如何创建一个嵌入 Houdini 面板系统的自定义 Python 工具面板。

---

## 一、什么是 Python Panel

Python Panel 是 Houdini 提供的一种机制，允许开发者使用 **Python + PySide6（Qt）** 创建自定义 UI 界面，并将其嵌入到 Houdini 的面板（Pane）系统中，与其他内置面板（Scene View、Parameter Editor 等）并列使用。

---

## 二、文件结构

```
$HOUDINI_USER_PREF_DIR/          ← 用户偏好目录（如 C:/Users/你的名字/Documents/houdini21.0/）
├── python_panels/
│   └── my_panel.pypanel          ← Python Panel 定义文件（在 Python Panel Editor 中编辑）
└── scripts/
    └── python/
        └── my_panel_gui.py       ← 界面逻辑代码（可选，也可全部写在 .pypanel 中）
```

---

## 三、版本兼容说明


| Houdini 版本         | Qt 版本    | Python Qt 绑定 |
| ------------------ | -------- | ------------ |
| 18.x / 19.x / 20.x | Qt 5     | PySide2      |
| **21.x 及以上**       | **Qt 6** | **PySide6**  |


> **注意：** 在 Houdini 21 中直接 `import PySide2` 会报 `ModuleNotFoundError`，必须使用 `PySide6`。

---

## 四、兼容性写法（推荐）

如果你的工具需要同时支持 Houdini 20 及以下（PySide2）和 Houdini 21+（PySide6），使用以下兼容写法：

```python
# 优先尝试导入 PySide6（Houdini 21+），失败则回退到 PySide2（Houdini 20 及以下）
try:
    from PySide6 import (
        QtWidgets,  # 提供所有 UI 控件，如按钮（QPushButton）、标签（QLabel）、布局（QVBoxLayout）等
        QtCore,     # 提供核心非图形功能，如信号与槽（Signal/Slot）、定时器（QTimer）、线程（QThread）等
        QtGui,      # 提供字体（QFont）、颜色（QColor）、图标（QIcon）、绘图（QPainter）等图形资源
    )   # Houdini 21+ 使用 PySide6
except ImportError:
    from PySide2 import (
        QtWidgets,  # 同上，PySide2 版本的 UI 控件模块
        QtCore,     # 同上，PySide2 版本的核心功能模块
        QtGui,      # 同上，PySide2 版本的图形资源模块
    )   # Houdini 20 及以下使用 PySide2
```

### 4.1 三个模块的常用内容

#### QtWidgets — UI 控件模块

> 所有可见的界面元素都来自这里。


| 类名               | 作用                          |
| ---------------- | --------------------------- |
| `QWidget`        | 所有控件的基类，自定义面板类需继承它          |
| `QLabel`         | 显示文本或图片的标签                  |
| `QPushButton`    | 可点击的按钮                      |
| `QLineEdit`      | 单行文本输入框                     |
| `QTextEdit`      | 多行富文本输入/显示框                 |
| `QPlainTextEdit` | 多行纯文本输入/显示框（性能优于 QTextEdit） |
| `QCheckBox`      | 复选框（勾选/取消勾选）                |
| `QRadioButton`   | 单选按钮（同组互斥）                  |
| `QComboBox`      | 下拉选择框                       |
| `QSlider`        | 滑动条                         |
| `QSpinBox`       | 整数数值输入框（带上下箭头）              |
| `QDoubleSpinBox` | 浮点数数值输入框                    |
| `QListWidget`    | 列表控件                        |
| `QTreeWidget`    | 树形控件                        |
| `QTableWidget`   | 表格控件                        |
| `QGroupBox`      | 带标题边框的控件容器                  |
| `QScrollArea`    | 可滚动区域容器                     |
| `QSplitter`      | 可拖拽分割线，将窗口分成多个区域            |
| `QTabWidget`     | 多标签页容器                      |
| `QDialog`        | 弹出对话框基类                     |
| `QMessageBox`    | 系统消息弹窗（提示/警告/错误）            |
| `QFileDialog`    | 文件/目录选择对话框                  |
| `QVBoxLayout`    | 垂直布局容器，子控件从上到下排列            |
| `QHBoxLayout`    | 水平布局容器，子控件从左到右排列            |
| `QGridLayout`    | 网格布局容器，按行列坐标放置控件            |
| `QFormLayout`    | 表单布局，左列标签右列控件成对排列           |
| `QSizePolicy`    | 控制控件在布局中的缩放策略               |
| `QSpacerItem`    | 布局中的弹性空白占位块                 |


#### QtCore — 核心功能模块

> 不涉及界面绘制，提供数据结构、事件、线程等底层机制。


| 类/枚举          | 作用                                          |
| ------------- | ------------------------------------------- |
| `Signal`      | 定义信号，用于事件通知（信号与槽机制的发射端）                     |
| `Slot`        | 装饰器，标记一个函数为槽函数（信号的接收端）                      |
| `QObject`     | 所有支持信号槽的对象的基类                               |
| `QTimer`      | 定时器，可周期性或延时触发函数                             |
| `QThread`     | 线程类，将耗时操作移到后台线程避免卡界面                        |
| `Qt`          | 全局枚举命名空间，如 `Qt.AlignCenter`、`Qt.Horizontal` |
| `QSize`       | 表示宽高的二维尺寸值                                  |
| `QPoint`      | 表示 x/y 坐标的点                                 |
| `QRect`       | 表示矩形区域（位置 + 尺寸）                             |
| `QUrl`        | 统一资源定位符，处理文件路径或网络地址                         |
| `QMimeData`   | 拖放操作中的数据容器                                  |
| `QSettings`   | 读写应用配置（注册表/ini 文件）                          |
| `QEvent`      | 事件基类，用于自定义事件处理                              |
| `QRunnable`   | 可在线程池中执行的任务基类                               |
| `QThreadPool` | 线程池，管理多个 QRunnable 任务                       |


#### QtGui — 图形资源模块

> 负责字体、颜色、图标、绘图等视觉资源，不直接是控件。


| 类名                            | 作用                       |
| ----------------------------- | ------------------------ |
| `QFont`                       | 字体设置（字体名、大小、加粗、斜体等）      |
| `QColor`                      | 颜色（支持 RGB、十六进制、HSV 等格式）  |
| `QPalette`                    | 控件的调色板，批量设置背景色/前景色       |
| `QIcon`                       | 图标，可从文件或资源加载             |
| `QPixmap`                     | 位图图像，适合显示到界面上            |
| `QImage`                      | 像素级图像操作（读写像素值）           |
| `QPainter`                    | 2D 绘图工具，在控件或图像上绘制线、形状、文字 |
| `QPen`                        | 画笔，控制线条颜色、宽度、样式          |
| `QBrush`                      | 画刷，控制填充颜色或图案             |
| `QCursor`                     | 鼠标光标样式                   |
| `QKeySequence`                | 快捷键序列，如 `Ctrl+S`         |
| `QValidator`                  | 输入验证器基类（配合 QLineEdit 使用） |
| `QIntValidator`               | 整数范围验证器                  |
| `QDoubleValidator`            | 浮点数范围验证器                 |
| `QRegularExpressionValidator` | 正则表达式验证器                 |
| `QDrag`                       | 拖放操作的发起对象                |


---

## 五、创建主界面类（my_panel_gui.py）

### 5.1 可放置的位置

`my_panel_gui.py` 并不限于一个固定位置，Houdini 会自动将以下目录加入 `sys.path`，在其中均可直接 `import`：


| 场景        | 路径                                       | 说明                                                                 |
| --------- | ---------------------------------------- | ------------------------------------------------------------------ |
| 个人工具（最常用） | `$HOUDINI_USER_PREF_DIR/scripts/python/` | Houdini 启动时自动加入 `sys.path`                                         |
| 随项目打包     | `$HIP/scripts/python/`                   | `$HIP` 为当前 `.hip` 文件所在目录，工具随项目走                                    |
| 工作室/团队共享  | `{HOUDINI_PATH自定义根目录}/scripts/python/`   | 在环境变量 `HOUDINI_PATH` 中添加自定义根目录，多人共用                                |
| 简单一次性工具   | 直接写在 `.pypanel` 文件中                      | 无需单独 `.py` 文件，代码全写在 `onCreateInterface()` 回调里                      |
| 特殊/临时路径   | 任意路径 + 手动加入 `sys.path`                   | 在 `.pypanel` 顶部执行 `sys.path.insert(0, "D:/my_tools/")` 后再 `import` |


> **推荐：** 个人开发放 `$HOUDINI_USER_PREF_DIR/scripts/python/`；需要随项目交付时改用 `$HIP/scripts/python/`。

### 5.2 布局容器说明

Qt 提供两种基础布局容器，实际开发中通常嵌套使用：


| 类名            | 方向  | 子控件排列方式                               |
| ------------- | --- | ------------------------------------- |
| `QVBoxLayout` | 垂直  | 每个子控件**独占一行**，从上到下依次排列；同一行内水平方向自动拉伸填满 |
| `QHBoxLayout` | 水平  | 每个子控件**并排一行**，从左到右依次排列                |


> 布局容器本身**不可见**，只负责管理内部控件的位置和大小，窗口缩放时自动重新计算。

**嵌套示例：** 想在同一行放多个控件，需在垂直布局内嵌套水平布局：

```
QVBoxLayout（垂直）
├── QLabel "标题"            ← 第一行，独占整行
├── QHBoxLayout（水平）      ← 第二行，内部再水平并排
│   ├── QPushButton "确定"
│   └── QPushButton "取消"
└── QLineEdit                ← 第三行，独占整行
```

对应代码：

```python
layout = QtWidgets.QVBoxLayout()         # 外层垂直布局，子控件从上到下排列

label = QtWidgets.QLabel("标题")
layout.addWidget(label)                  # 第一行：标签独占整行

btn_row = QtWidgets.QHBoxLayout()        # 内层水平布局，子控件从左到右并排
btn_row.addWidget(QtWidgets.QPushButton("确定"))
btn_row.addWidget(QtWidgets.QPushButton("取消"))
layout.addLayout(btn_row)               # 将整个水平布局作为一行加入垂直布局

edit = QtWidgets.QLineEdit()
layout.addWidget(edit)                   # 第三行：输入框独占整行
```

### 5.3 代码

将以下代码保存为 `my_panel_gui.py`，放入 **5.1 节表格**中所选的路径（下方示例以最常用的用户偏好目录为准）：

```
$HOUDINI_USER_PREF_DIR/scripts/python/my_panel_gui.py
```

```python
# 导入兼容性 Qt 库：优先 PySide6（Houdini 21+），回退 PySide2（Houdini 20 及以下）
try:
    from PySide6 import QtWidgets, QtCore, QtGui
except ImportError:
    from PySide2 import QtWidgets, QtCore, QtGui


class MyPanelGUI(QtWidgets.QWidget):
    """
    自定义 Houdini Python Panel 的主界面类。
    继承自 QWidget，Houdini 会将此对象嵌入面板容器中显示。
    """

    def __init__(self, parent=None):
        """
        构造函数。
        parent: QWidget 或 None，Qt 父对象，通常由 Houdini 自动管理，传 None 即可
        """
        super(MyPanelGUI, self).__init__(parent)  # 调用父类 QWidget 的构造函数

    def build_main_gui(self, pane_tab):
        """
        构建并初始化界面布局，由 onCreateInterface() 回调调用。
        pane_tab: hou.PaneTab 对象，代表当前面板标签。
                  本示例中仅保存引用备用，未直接与其交互；
                  若需响应 Houdini 上下文（如获取当前选中节点、当前帧），直接调用 hou 全局接口即可，无需经过 self.pane_tab；
                  self.pane_tab 仅在需要区分多面板来源或操控面板自身时才有用。
        """
        self.pane_tab = pane_tab                      # 保存 pane_tab 引用备用；本示例暂未使用，留作扩展入口

        layout = QtWidgets.QVBoxLayout()              # 创建垂直布局容器（不可见，只管排列规则）；其内的子控件（按钮/标签/输入框等具体 UI 元素）按添加顺序从上到下依次排列，窗口缩放时自动重新计算位置

        # ---------- 标题标签 ----------
        label = QtWidgets.QLabel("我的自定义面板")     # 创建文本标签，显示面板标题
        label.setAlignment(QtCore.Qt.AlignmentFlag.AlignCenter)  # 设置文本居中对齐（PySide6 枚举写法）
        layout.addWidget(label)                       # 将标签添加到垂直布局中

        # ---------- 输入框 ----------
        self.line_edit = QtWidgets.QLineEdit()        # 创建单行文本输入框
        self.line_edit.setPlaceholderText("请输入消息内容...")  # 设置输入框为空时显示的提示文字
        layout.addWidget(self.line_edit)              # 将输入框添加到布局中

        # ---------- 按钮区（多按钮：每个按钮绑定独立槽函数，逻辑清晰互不干扰）----------
        btn_send  = QtWidgets.QPushButton("发送消息到 Houdini")  # 创建"发送"按钮
        btn_clear = QtWidgets.QPushButton("清空输入框")           # 创建"清空"按钮

        btn_send.clicked.connect(self.on_send_clicked)    # 点击"发送"时触发 on_send_clicked
        btn_clear.clicked.connect(self.on_clear_clicked)  # 点击"清空"时触发 on_clear_clicked

        btn_row = QtWidgets.QHBoxLayout()                 # 水平布局容器，让两个按钮并排在同一行
        btn_row.addWidget(btn_send)                       # 将"发送"按钮加入水平行
        btn_row.addWidget(btn_clear)                      # 将"清空"按钮加入水平行
        layout.addLayout(btn_row)                         # 将整个按钮行作为一行加入垂直布局

        # ---------- 弹性空白 ----------
        layout.addStretch()                           # 在底部添加弹性空间，将控件推到顶部

        self.setLayout(layout)                        # 将布局应用到当前 QWidget 上

    def on_send_clicked(self):
        """
        "发送"按钮的槽函数，点击时读取输入框内容并通过 Houdini API 弹出消息框。
        多按钮场景推荐为每个按钮定义独立槽函数，避免在一个函数内用 if 判断来源。
        """
        import hou                                    # 在函数内部导入 hou，避免在 Houdini 外部运行时报错

        text = self.line_edit.text()                  # 获取输入框中当前的文本内容（str 类型）

        if text.strip():                              # 判断文本去除首尾空格后是否非空
            hou.ui.displayMessage(text)               # 调用 Houdini UI API 弹出对话框显示消息
        else:
            hou.ui.displayMessage("输入框为空！")      # 若输入为空，提示用户

    def on_clear_clicked(self):
        """
        "清空"按钮的槽函数，点击时清除输入框内所有文本。
        """
        self.line_edit.clear()                        # 清空输入框内容

    def cleanup(self):
        """
        资源清理函数，由 onDestroyInterface() 回调调用。
        在这里断开信号连接、释放文件句柄、停止线程等。
        """
        pass                                          # 当前示例无需额外清理，保留接口供扩展
```

### 5.4 关于 pane_tab 与 Houdini 上下文

**Houdini 上下文**指 Houdini 当前的运行状态，例如用户选中了哪个节点、时间轴当前在第几帧、激活的是哪个网络视图等。

响应 Houdini 上下文变化**不需要 `pane_tab`**，直接调用 `hou` 的全局接口即可。`pane_tab` 只在以下两种场景才有用：

1. **多面板共存时区分来源** —— 判断用户操作的是哪个面板
2. **操控面板自身** —— 如 `pane_tab.setName()`、`pane_tab.close()` 等

```python
# 获取当前 Houdini 场景中所有被选中的节点（返回 tuple[hou.Node]）
selected_nodes = hou.selectedNodes()

# 获取当前时间轴帧号（返回 float）
current_frame = hou.frame()

# 通过 pane_tab 获取所属桌面，再找到场景视图面板（多面板时用于区分来源）
scene_viewer = self.pane_tab.pane().desktop().paneTabOfType(hou.paneTabType.SceneViewer)
```

**拿到节点对象（`hou.Node`）之后可以做什么：**

```python
node = selected_nodes[0]   # 取第一个选中的节点

# ── 读取节点信息 ──────────────────────────────────────────
node.name()                  # 节点名称（str），如 "geo1"
node.type().name()           # 节点类型名（str），如 "geo"、"attribwrangle"
node.path()                  # 节点在场景中的完整路径（str），如 "/obj/geo1"
node.comment()               # 节点注释文字（str）
node.isDisplayFlagSet()      # 是否开启 Display 标志（bool，即视图中的"眼睛"图标）

# ── 读写参数（Parm） ──────────────────────────────────────
node.parm("tx").eval()               # 读取单个参数的当前值（float/int/str，取决于参数类型）
node.parm("tx").set(5.0)             # 将单个参数设为指定值
node.parmTuple("t").set((1, 2, 3))   # 批量设置 tx/ty/tz 三个分量

# ── 遍历连接关系 ──────────────────────────────────────────
node.children()    # 所有子节点（tuple[hou.Node]），适用于 Geometry/Object 等容器节点
node.inputs()      # 所有输入节点（tuple[hou.Node]，无连接处为 None）
node.outputs()     # 所有输出节点（tuple[hou.Node]）
node.parent()      # 父节点（hou.Node）

# ── 执行操作 ──────────────────────────────────────────────
node.cook()                  # 强制重新计算该节点
node.setSelected(True)       # 在视图中选中该节点
node.setDisplayFlag(True)    # 打开 Display 标志（让该节点的几何体显示在视图中）
node.setName("new_name")     # 重命名节点
node.destroy()               # 删除该节点（不可撤销，谨慎使用）
```

**典型面板用例 — 批量修改选中节点的参数：**

```python
def on_send_clicked(self):
    """点击按钮后，将所有选中节点的 tx 参数统一设为输入框中填写的值。"""
    import hou

    text = self.line_edit.text().strip()    # 获取输入框文本并去除首尾空格（str）
    if not text:
        hou.ui.displayMessage("请输入数值")
        return

    value = float(text)                     # 将文本转为浮点数（float）

    nodes = hou.selectedNodes()             # 获取当前所有选中节点（tuple[hou.Node]）
    if not nodes:
        hou.ui.displayMessage("没有选中任何节点")
        return

    for node in nodes:                      # 遍历每个选中节点
        node.parm("tx").set(value)          # 将该节点的 tx 参数设为指定值

    hou.ui.displayMessage(f"已将 {len(nodes)} 个节点的 tx 设为 {value}")
```

**在槽函数中使用的实际示例：** 点击按钮时读取当前选中节点并弹窗显示：

```python
def on_send_clicked(self):
    """点击"发送"按钮时，读取 Houdini 中当前选中的节点名称并弹窗显示。"""
    import hou

    nodes = hou.selectedNodes()          # 获取当前选中节点列表（tuple，可能为空）
    if nodes:
        hou.ui.displayMessage(nodes[0].name())    # 弹窗显示第一个节点的名称（str）
    else:
        hou.ui.displayMessage("没有选中任何节点")
```

> `**pane_tab` 的典型用途：** 当界面中存在多个 Python Panel 面板时，用它来判断"是哪个面板发出的事件"，从而做出不同响应。单面板场景下直接用 `hou` 全局接口更简洁。

---

## 六、编写 .pypanel 脚本（在 Python Panel Editor 中填写）

打开 `Windows → Python Panel Editor`，在 **Script** 区域填入以下代码：

```python
# 导入自定义界面模块（文件可放置的位置见 5.1 节，需确保所在目录已在 sys.path 中）
import my_panel_gui

# 全局变量，保存界面实例，防止被 Python 垃圾回收
the_panel = None


def onCreateInterface():
    """
    Houdini 创建面板时自动调用的回调函数。
    必须返回一个 QWidget 实例，Houdini 将其嵌入面板容器中显示。
    kwargs 是 Houdini 自动注入的字典，包含 'paneTab' 等键。
    """
    global the_panel                                      # 声明使用全局变量

    pane_tab = kwargs["paneTab"]                          # 从 Houdini 注入的 kwargs 中获取当前 PaneTab 对象（hou.PaneTab 类型）
                                                          # PaneTab 代表 Houdini 界面中的一个"面板标签"，即用户在 UI 中打开的那个标签页
                                                          # 用途：区分多面板来源（pane_tab.type()）、操控面板自身（setName/close 等）、
                                                          #       获取所属桌面（pane_tab.pane().desktop()）
                                                          # 注意：获取选中节点/当前帧等上下文信息直接用 hou 全局接口，无需经过 pane_tab

    the_panel = my_panel_gui.MyPanelGUI()                 # 实例化自定义界面类，得到一个 QWidget 对象
    the_panel.build_main_gui(pane_tab)                    # 调用构建方法，传入 pane_tab 以便与 Houdini 交互

    return the_panel                                      # 返回 QWidget，Houdini 将其嵌入面板 —— 这一步是关键！


def onDestroyInterface():
    """
    Houdini 销毁面板时自动调用的回调函数。
    在此处执行资源清理，防止内存泄漏。
    """
    global the_panel                                      # 声明使用全局变量

    if the_panel is not None:                             # 判断面板实例是否存在，避免空指针错误
        the_panel.cleanup()                               # 调用自定义清理方法，释放资源
```

---

## 七、Python Panel Editor 配置步骤

### 7.1 打开编辑器并新建

1. 打开菜单：`Windows` → `Python Panel Editor`
2. 顶部下拉框显示当前编辑的 Panel，格式为 `Label (Name)`
3. 点击 **New Interface** 新建，或 **Delete Interfaces...** 删除

---

### 7.2 基本信息区参数


| 参数                              | 示例值                    | 说明                                                                                                                                                                                                                                                         |
| ------------------------------- | ---------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Save To**                     | `.../my_panel.pypanel` | `.pypanel` 文件的保存路径，可手动修改；Houdini 会自动扫描 `HOUDINI_PATH` 下所有 `python_panels/` 子目录，可选位置如下： • `$HOUDINI_USER_PREF_DIR/python_panels/` — 个人工具，Editor 默认保存位置（推荐） • `$HIP/python_panels/` — 随 `.hip` 项目文件一起打包交付 • `{HOUDINI_PATH自定义根目录}/python_panels/` — 工作室/团队共享 |
| **Original interface file**     | 同上（只读）                 | 提示该 Panel 最初从哪个文件加载，只读不可编辑                                                                                                                                                                                                                                 |
| **Name**                        | `my_custom_panel`      | 内部唯一标识符，代码中通过 `hou.pypanel.interfaceByName("my_custom_panel")` 引用，**不能包含空格**                                                                                                                                                                               |
| **Label**                       | `我的自定义面板`              | 在 Houdini UI（菜单、标签页标题）中显示的名称，**可以包含空格和中文**                                                                                                                                                                                                                 |
| **Icon**                        | `MISC_python`          | 面板图标，填写 Houdini 内置图标名称（可在 Icon Browser 中查找），留空也可                                                                                                                                                                                                           |
| **Show Network Navigation Bar** | 未勾选                    | 是否在面板顶部显示**网络路径导航栏**。Houdini 很多内置面板（如 Node Graph）顶部都有一条形如 `/obj/geo1 > sopnet > ...` 的路径导航栏，点击可跳转上层节点，类似文件浏览器的地址栏。 • **勾选** → 顶部出现导航栏，适合需要跟随网络层级变化的工具（如自定义节点浏览器） • **不勾选** → 顶部只显示自己写的 UI，适合大多数自定义工具（批量操作面板、计算工具等）                                         |


---

### 7.3 Menu Hints（菜单提示）区参数

控制该 Panel **出现在哪些 UI 入口**，两侧可独立配置。

#### 左侧：Toolbar Menu（顶部工具栏菜单）


| 参数                             | 说明                                                                                    |
| ------------------------------ | ------------------------------------------------------------------------------------- |
| **Include in Toolbar Menu**    | 勾选后，当**磁盘上没有加载自定义 toolbar menu 定义文件**时，Panel 会被加入 toolbar menu；若已有定义文件则此选项被忽略，以文件为准   |
| **Menu Position**              | 在菜单中的**相对**排列顺序，并非绝对位置，而是与其他面板的 Position 值互相比较后排序。例如：面板 A=4、B=1、C=25，则显示顺序为 B → A → C |
| **Create Preceding Separator** | 勾选后在此菜单项**之前**插入一条分隔线，用于视觉分组                                                          |


#### 右侧：Pane Tab Menu（面板标签菜单）


| 参数                             | 说明                                                    |
| ------------------------------ | ----------------------------------------------------- |
| **Include in Pane Tab Menu**   | 勾选后，在任意面板右上角 **+** 号菜单中可以找到并嵌入此 Panel，**最常用的入口，必须勾选** |
| **Menu Position**              | 在 + 号菜单中的排列顺序，数字越小越靠前                                 |
| **Create Preceding Separator** | 是否在此菜单项之前插入一条分隔线                                      |


---

### 7.4 Parameters Pane Hints（参数面板提示）区参数

> 此区域用于将 Python Panel 作为**节点的参数面板**替代品，普通工具面板通常不需要配置。


| 参数                          | 说明                                                   |
| --------------------------- | ---------------------------------------------------- |
| **Show in Parameters Pane** | 勾选后，此 Panel 可在参数面板区域显示，需配合下方 For Operators 使用        |
| **For Operators**           | 指定哪些节点类型（Operator）激活时使用此 Panel 替代默认参数面板，需先勾选上方选项才可编辑 |


---

### 7.5 完成配置

1. 在底部 **Script** 区域粘贴第六节的回调代码
2. 点击 **Apply** 应用更改（不关闭窗口）
3. 点击 **Accept** 保存并关闭

> **注意：** 修改后必须点击 **Apply** 或 **Accept**，否则所有更改不会生效。

---

## 八、打开面板的三种方式

### 方式 1：通过面板 + 号菜单（最常用）

在任意 Houdini 面板右上角点击 **+** 号，在弹出菜单中找到并点击你的面板名称即可嵌入。

> 前提：必须在 Python Panel Editor 中勾选了 `Include in Pane Tab Menu`。

### 方式 2：通过 Toolbar Menu

勾选 `Include in Toolbar Menu` 后，Panel 会被加入 Houdini 的 toolbar menu。

> **注意（官方文档说明）：** 此选项仅在**磁盘上没有加载自定义 toolbar menu 定义文件**时生效；若已存在定义文件，则以文件内容为准，此勾选项会被忽略。
>
> **为什么勾选了却找不到入口？**
> Houdini 自带的 toolbar menu 定义文件默认已经加载，导致勾选项被忽略，Panel 不会自动出现。若要强制加入，需要手动编辑该定义文件，将 Panel 添加进去。
>
> **实际建议：** 不必纠结 Toolbar Menu，通过 **Pane Tab**（方式 1）打开面板是更标准的使用方式，Houdini 所有内置面板（Scene View、Node Graph 等）也都是这样使用的，Toolbar Menu 只是可选的额外入口。

### 方式 3：通过 Python 代码打开

```python
import hou

desktop = hou.ui.curDesktop()                            # 获取当前桌面对象（hou.Desktop 类型）

# 创建一个浮动面板窗口，类型指定为 PythonPanel
panel = desktop.createFloatingPanel(hou.paneTabType.PythonPanel)

# 获取浮动面板中的 PythonPanel 标签对象
tab = panel.paneTabOfType(hou.paneTabType.PythonPanel)

# 通过 Name 字段（在 Python Panel Editor 中填写的）找到并激活你的 Panel
tab.setActiveInterface(hou.pypanel.interfaceByName("my_custom_panel"))
```

---

## 九、常见报错及解决方法

### 报错 1：`ModuleNotFoundError: No module named 'PySide2'`

**原因：** Houdini 21 已使用 PySide6，不再内置 PySide2。

**解决：** 将所有 `from PySide2 import ...` 改为 `from PySide6 import ...`

---

### 报错 2：找不到自定义 Panel（+ 号菜单中没有）

**排查步骤：**

```python
# 在 Houdini Python Shell 中执行，检查 Panel 是否被加载
import hou
print(hou.pypanel.interfaces())   # 输出所有已加载的 Panel 接口列表
```

若列表中没有你的 Panel，依次检查：

1. 是否勾选了 `Include in Pane Tab Menu` 并点击了 Apply
2. `.pypanel` 文件是否在正确目录：`$HOUDINI_USER_PREF_DIR/python_panels/`
3. 脚本是否有语法错误（查看 Houdini 控制台输出）
4. 尝试重启 Houdini

---

### 报错 3：`my_panel_gui` 模块找不到

**解决：** 在 `.pypanel` 脚本顶部手动添加路径：

```python
import sys
sys.path.insert(0, "D:/你的工具目录")    # 替换为 my_panel_gui.py 所在的实际目录路径
import my_panel_gui                      # 路径添加后即可正常导入
```

---

### PySide6 与 PySide2 的主要语法差异


| 功能           | PySide2 写法             | PySide6 写法                         |
| ------------ | ---------------------- | ---------------------------------- |
| 对话框执行        | `dialog.exec_()`       | `dialog.exec()`                    |
| QAction 所在模块 | `QtWidgets.QAction`    | `QtGui.QAction`                    |
| 枚举值          | `QtCore.Qt.Horizontal` | `QtCore.Qt.Orientation.Horizontal` |


---

## 十、自定义功能开发

界面显示出来后，可按以下三个层次逐步添加功能。

---

### 10.1 第一层：基础交互（很简单）

普通 Qt 控件 + `hou` API 即可实现，难度等同于写普通 Python 脚本。

```python
def on_click(self):
    """点击按钮时，选中场景中所有几何体节点。"""
    import hou                                                        # 导入 Houdini API 模块

    obj_node = hou.node("/obj")                                       # 获取 /obj 路径下的节点对象
    # 遍历 /obj 下所有子节点，筛选出类型为 "geo" 的几何体节点，返回列表
    geo_nodes = [n for n in obj_node.children() if n.type().name() == "geo"]

    hou.selectNodes(geo_nodes)                                        # 在场景视图中选中这些节点
```

**常用 `hou` API 速查：**


| 功能           | 代码示例                                                    |
| ------------ | ------------------------------------------------------- |
| 获取当前选中节点     | `hou.selectedNodes()` → 返回 `tuple[hou.Node]`            |
| 读取节点参数值      | `node.parm("tx").eval()` → 返回参数当前值（float/int/str）       |
| 设置节点参数值      | `node.parm("tx").set(1.0)`                              |
| 在 /obj 下创建节点 | `hou.node("/obj").createNode("geo")` → 返回新建的 `hou.Node` |
| 弹出消息对话框      | `hou.ui.displayMessage("消息内容")`                         |
| 获取当前帧号       | `hou.frame()` → 返回 `float`                              |
| 设置当前帧        | `hou.setFrame(24)`                                      |


---

### 10.2 第二层：响应 Houdini 事件（中等难度）

让面板**实时响应场景变化**，需要注册事件回调。

```python
def build_main_gui(self, pane_tab):
    """构建界面，同时注册 Houdini 事件监听。"""
    # --- 界面部分 ---
    layout = QtWidgets.QVBoxLayout()                                  # 创建垂直布局容器（不可见，只管排列规则）；子控件按添加顺序从上到下排列，窗口缩放时自动重新计算位置

    self.label = QtWidgets.QLabel("未选中任何节点")                    # 创建显示选中节点名称的标签
    layout.addWidget(self.label)                                      # 将标签加入布局

    self.setLayout(layout)                                            # 应用布局到 QWidget

    # --- 事件注册部分 ---
    import hou
    # 注册选中状态变化的回调，当用户在场景中选中/取消节点时自动触发
    hou.selectionChanged.addEventCallback(self.on_selection_changed)

def on_selection_changed(self, event_type, **kwargs):
    """
    选中节点变化时的回调函数。
    event_type: hou.selectionChangeReason 枚举，表示变化原因
    kwargs:     Houdini 传入的附加信息字典
    """
    import hou
    nodes = hou.selectedNodes()                                       # 获取当前所有选中节点，返回 tuple[hou.Node]
    names = [n.name() for n in nodes]                                 # 提取每个节点的名称，返回 list[str]

    if names:
        self.label.setText("选中：" + ", ".join(names))               # 更新标签文字为节点名称列表
    else:
        self.label.setText("未选中任何节点")                           # 无选中时显示提示文字

def cleanup(self):
    """
    面板销毁时移除所有事件回调。
    必须执行，否则面板关闭后回调仍然存在，访问已销毁的 Qt 控件会导致 Houdini 崩溃。
    """
    import hou
    hou.selectionChanged.removeEventCallback(self.on_selection_changed)  # 移除选中变化回调
```

**常用 Houdini 事件：**


| 事件                                   | 触发时机             |
| ------------------------------------ | ---------------- |
| `hou.selectionChanged`               | 场景中节点选中状态变化      |
| `hou.nodeEventType.NameChanged`      | 节点被重命名           |
| `hou.nodeEventType.ParmTupleChanged` | 节点参数值被修改         |
| `hou.hipFileEventType.AfterLoad`     | `.hip` 场景文件加载完成后 |
| `hou.playbar.addEventCallback`       | 时间轴当前帧发生变化       |


> **重要：** 凡是在 `build_main_gui` 中注册的回调，**必须在 `cleanup` 中对应移除**，否则会导致 Houdini 崩溃。

---

### 10.3 第三层：复杂业务工具（难度在业务逻辑本身）

例如批量重命名节点、自动生成材质网络、渲染任务提交等。UI 框架写法不变，难度主要在对 Houdini 节点体系的理解。

```python
def batch_rename_nodes(self):
    """
    批量重命名选中节点：在原名称后追加 _v2 后缀。
    """
    import hou

    nodes = hou.selectedNodes()                                       # 获取所有选中节点

    # 使用撤销块，让整个批量操作可以被 Ctrl+Z 一次性撤销
    with hou.undos.group("Batch Rename"):                             # 创建撤销组，参数为撤销历史中显示的名称
        for node in nodes:                                            # 遍历每个选中节点
            old_name = node.name()                                    # 获取节点当前名称（str 类型）
            new_name = old_name + "_v2"                               # 拼接新名称
            node.setName(new_name, unique_name=True)                  # 设置新名称，unique_name=True 自动处理重名
```

---

### 10.4 实战示例：PCG 控制面板

Panel 本身不负责生成逻辑，而是作为**控制台**驱动节点网络参数。

```
┌─────────────────────────┐
│    Python Panel（控制台） │  ← 用户在这里调参、点击生成
│  - 参数滑块 / 输入框      │
│  - 预设选择              │
│  - 生成按钮              │
└────────────┬────────────┘
             │ 读写参数 / 触发 cook
             ▼
┌─────────────────────────┐
│   Houdini 节点网络       │  ← 真正的 PCG 逻辑在这里
│  /obj/pcg_asset/        │
│   ├── scatter_sop       │
│   ├── copy_to_points    │
│   └── ...               │
└─────────────────────────┘
```

```python
try:
    from PySide6 import QtWidgets, QtCore
except ImportError:
    from PySide2 import QtWidgets, QtCore


class MyPanelGUI(QtWidgets.QWidget):
    """PCG 控制面板主界面类，继承自 QWidget。"""

    TARGET_NODE_PATH = "/obj/pcg_asset/scatter1"    # PCG 目标节点路径，修改此常量指向你的 scatter 节点

    def __init__(self, parent=None):
        """构造函数，parent 为 Qt 父对象，传 None 即可。"""
        super(MyPanelGUI, self).__init__(parent)    # 调用父类构造函数

    def buildMainGUI(self, pane_tab):
        """构建 PCG 控制面板的界面布局，pane_tab 为 hou.PaneTab 对象。"""
        layout = QtWidgets.QVBoxLayout()            # 创建垂直布局容器（不可见，只管排列规则）；子控件按添加顺序从上到下排列，窗口缩放时自动重新计算位置

        # ── 散布数量 ──────────────────────────────
        count_row = QtWidgets.QHBoxLayout()                     # 水平行布局
        count_row.addWidget(QtWidgets.QLabel("散布数量"))        # 标签
        self.spin_count = QtWidgets.QSpinBox()                  # 整数输入框
        self.spin_count.setRange(1, 10000)                      # 允许输入范围：1 ~ 10000
        self.spin_count.setValue(500)                           # 默认值 500
        count_row.addWidget(self.spin_count)
        layout.addLayout(count_row)

        # ── 随机种子 ──────────────────────────────
        seed_row = QtWidgets.QHBoxLayout()
        seed_row.addWidget(QtWidgets.QLabel("随机种子"))
        self.spin_seed = QtWidgets.QSpinBox()                   # 种子整数输入框
        self.spin_seed.setRange(0, 99999)                       # 种子范围：0 ~ 99999
        self.spin_seed.setValue(0)                              # 默认种子为 0
        seed_row.addWidget(self.spin_seed)
        layout.addLayout(seed_row)

        # ── 缩放范围 ──────────────────────────────
        scale_row = QtWidgets.QHBoxLayout()
        scale_row.addWidget(QtWidgets.QLabel("缩放范围"))
        self.spin_scale_min = QtWidgets.QDoubleSpinBox()        # 最小缩放浮点输入框
        self.spin_scale_min.setRange(0.01, 10.0)               # 范围 0.01 ~ 10
        self.spin_scale_min.setValue(0.8)                      # 默认最小缩放 0.8
        self.spin_scale_max = QtWidgets.QDoubleSpinBox()        # 最大缩放浮点输入框
        self.spin_scale_max.setRange(0.01, 10.0)
        self.spin_scale_max.setValue(1.2)                      # 默认最大缩放 1.2
        scale_row.addWidget(self.spin_scale_min)
        scale_row.addWidget(QtWidgets.QLabel("~"))              # 分隔符
        scale_row.addWidget(self.spin_scale_max)
        layout.addLayout(scale_row)

        # ── 分隔线 ────────────────────────────────
        line = QtWidgets.QFrame()                               # 水平分隔线控件
        line.setFrameShape(QtWidgets.QFrame.Shape.HLine)        # 设置为水平线样式
        layout.addWidget(line)

        # ── 生成按钮 ──────────────────────────────
        btn_generate = QtWidgets.QPushButton("生成 PCG")        # 生成触发按钮
        btn_generate.clicked.connect(self.on_generate)         # 点击时调用 on_generate
        layout.addWidget(btn_generate)

        # ── 状态提示标签 ──────────────────────────
        self.label_status = QtWidgets.QLabel("就绪")            # 操作结果状态文字
        layout.addWidget(self.label_status)

        layout.addStretch()                                     # 底部弹性空间，控件靠上排列
        self.setLayout(layout)                                  # 应用布局

    def on_generate(self):
        """点击"生成 PCG"按钮时触发，将面板参数写入节点并强制重算。"""
        import hou

        node = hou.node(self.TARGET_NODE_PATH)                  # 根据路径获取目标节点（hou.Node）

        if node is None:                                        # 节点不存在时给出错误提示
            self.label_status.setText("错误：找不到节点 " + self.TARGET_NODE_PATH)
            return

        count = self.spin_count.value()                         # 获取散布数量（int）
        seed  = self.spin_seed.value()                          # 获取随机种子（int）
        s_min = self.spin_scale_min.value()                     # 获取最小缩放（float）
        s_max = self.spin_scale_max.value()                     # 获取最大缩放（float）

        with hou.undos.group("PCG Generate"):                   # 撤销块：所有修改可一次性 Ctrl+Z 撤销
            node.parm("npts").set(count)                        # 写入散布点数量参数
            node.parm("seed").set(seed)                         # 写入随机种子参数
            node.parm("scale_min").set(s_min)                   # 写入最小缩放参数
            node.parm("scale_max").set(s_max)                   # 写入最大缩放参数
            node.cook(force=True)                               # 强制重新计算节点，force=True 忽略缓存

        self.label_status.setText(f"已生成：{count} 个点，种子 {seed}")  # 更新状态文字

    def cleanup(self):
        """面板销毁时的清理函数。"""
        pass
```

**PCG 功能难度评估：**


| 功能       | 难度  | 说明                            |
| -------- | --- | ----------------------------- |
| 控制节点参数   | 低   | `node.parm("x").set(v)` 即可    |
| 触发节点计算   | 低   | `node.cook(force=True)`       |
| 撤销支持     | 低   | 套 `with hou.undos.group()`    |
| 实时预览     | 中   | 监听控件 `valueChanged` 信号（见下方说明） |
| 管理预设     | 中   | 用 JSON 文件读写参数组合               |
| 生成复杂节点网络 | 中高  | 需熟悉 Houdini 节点体系              |


#### 实时预览说明

**实时预览**指用户拖动滑块或修改数值时，Houdini 场景**立刻同步更新**，无需点击确认按钮。

实现方式是将槽函数连接到控件的 `valueChanged` 信号，该信号在数值每次改变时自动触发：

```python
slider = QtWidgets.QSlider(QtCore.Qt.Horizontal)   # 创建水平滑块

# 将 valueChanged 信号连接到槽函数，每次数值变化时立即触发
# valueChanged 信号会将当前数值（int）作为参数传入槽函数
slider.valueChanged.connect(self.on_slider_changed)

def on_slider_changed(self, value):
    """滑块数值变化时调用，实现实时预览。value 为滑块当前整数值。"""
    import hou
    node = hou.node("/obj/geo1")          # 获取目标节点
    node.parm("tx").set(float(value))     # 实时将节点 tx 参数同步为滑块当前值
```

`**valueChanged` 与 `clicked` 的区别：**


| 信号             | 触发时机        | 适合场景      |
| -------------- | ----------- | --------- |
| `clicked`      | 点击按钮后触发一次   | 确认操作、批量处理 |
| `valueChanged` | 数值每改变一次立即触发 | 实时预览、参数调节 |


---

### 10.5 在 Panel 中使用 VEX 逻辑

VEX 本身无法脱离节点独立运行，Panel 与 VEX 配合的核心思路是：**Python 将 VEX 代码字符串写入 Wrangle 节点的 `snippet` 参数，由节点负责执行**。

#### 方式一：动态修改 Wrangle 节点的 VEX 代码（最常用）

```python
def apply_vex(self):
    """将面板中输入的 VEX 代码写入 Wrangle 节点并触发执行。"""
    import hou

    wrangle = hou.node("/obj/geo1/attribwrangle1")    # 获取场景中已有的 Attribute Wrangle 节点（hou.Node）

    if wrangle is None:
        return

    # VEX 代码写成 Python 多行字符串，可根据面板参数动态拼接
    scale_val = self.spin_scale.value()               # 从面板 SpinBox 获取缩放值（float）

    vex_code = f"""
// 根据面板传入的缩放值修改每个点的位置
float scale = {scale_val};    // 从 Python Panel 动态注入的缩放参数
@P *= scale;                  // 将每个点的位置向量乘以缩放值
@Cd = set(scale, 0, 0);       // 根据缩放值设置点颜色（红色通道）
"""                                                   # f-string 将 Python 变量嵌入 VEX 代码字符串中

    with hou.undos.group("Apply VEX"):                # 撤销块，修改可 Ctrl+Z 还原
        wrangle.parm("snippet").set(vex_code)         # 将 VEX 字符串写入 Wrangle 的 snippet 参数
        wrangle.cook(force=True)                      # 强制重算节点，执行写入的 VEX 代码
```

#### 方式二：用 Python 动态创建 Wrangle 节点并注入 VEX

```python
def create_wrangle_with_vex(self):
    """在节点网络中新建一个 Wrangle 节点，并注入自定义 VEX 逻辑。"""
    import hou

    geo = hou.node("/obj/geo1")                       # 获取 geo 节点（hou.Node），作为父节点

    # 在 geo 节点内部创建一个 Attribute Wrangle SOP 节点
    wrangle = geo.createNode("attribwrangle", "my_vex_wrangle")  # 返回新建的 hou.Node

    vex_code = """
// 为每个点添加随机偏移，实现散布效果
int seed = @ptnum;                          // 使用点序号作为随机种子，每个点结果不同
vector rand_offset = fit01(rand(seed), -1, 1);  // 生成 -1~1 之间的随机向量
@P += rand_offset * chf("strength");       // 用 channel 引用面板可控的强度参数
"""                                                   # VEX 代码字符串，@P 是点位置属性

    wrangle.parm("snippet").set(vex_code)             # 将 VEX 代码写入节点的 snippet 参数
    wrangle.parm("class").set(0)                      # 设置属性作用域：0=点级别（point）

    wrangle.setDisplayFlag(True)                      # 设置该节点为显示节点（场景视图中可见）
    wrangle.setRenderFlag(True)                       # 设置该节点为渲染节点
```

#### 方式三：从面板文本框实时输入 VEX 代码

```python
def buildMainGUI(self, pane_tab):
    """构建含 VEX 代码编辑器的面板布局。"""
    import hou
    layout = QtWidgets.QVBoxLayout()              # 创建垂直布局容器（不可见，只管排列规则）；子控件按添加顺序从上到下排列，窗口缩放时自动重新计算位置

    # 多行文本框，供用户直接在面板中编写 VEX 代码
    self.vex_editor = QtWidgets.QPlainTextEdit()                   # 多行纯文本编辑器控件
    self.vex_editor.setPlaceholderText("在此输入 VEX 代码...")     # 空内容时的提示文字
    self.vex_editor.setFont(                                       # 设置等宽字体，适合代码显示
        __import__("PySide6.QtGui", fromlist=["QFont"]).QFont("Courier New", 10)
    )
    # 填入默认 VEX 代码模板
    self.vex_editor.setPlainText("@P.y += sin(@P.x * 2) * chf(\"amplitude\");")
    layout.addWidget(self.vex_editor)                              # 将编辑器加入布局

    btn_apply = QtWidgets.QPushButton("应用 VEX")                  # 应用按钮
    btn_apply.clicked.connect(self.on_apply_vex)                  # 点击时触发应用逻辑
    layout.addWidget(btn_apply)

    self.setLayout(layout)

def on_apply_vex(self):
    """读取文本框中的 VEX 代码并写入目标 Wrangle 节点。"""
    import hou

    vex_code = self.vex_editor.toPlainText()                      # 获取文本框全部内容（str 类型）

    wrangle = hou.node("/obj/geo1/attribwrangle1")                # 获取目标 Wrangle 节点
    if wrangle is None:
        return

    with hou.undos.group("Apply VEX from Panel"):                 # 撤销块
        wrangle.parm("snippet").set(vex_code)                     # 写入 VEX 代码到 snippet 参数
        wrangle.cook(force=True)                                  # 强制重算执行 VEX
```

**VEX 与 Panel 配合方式总结：**


| 方式             | 适用场景      | 说明                             |
| -------------- | --------- | ------------------------------ |
| 动态拼接 VEX 字符串   | 参数驱动的固定逻辑 | 用 f-string 把 Panel 参数注入 VEX 代码 |
| 创建新 Wrangle 节点 | 一键生成工具    | Python 自动建节点并注入逻辑              |
| 面板内置代码编辑器      | 高级用户工具    | 用 `QPlainTextEdit` 做简易 VEX 编辑器 |


> **核心原则：** VEX 必须运行在 Wrangle 节点内，Python Panel 通过 `node.parm("snippet").set(vex_code)` 控制 VEX 内容，通过 `node.cook(force=True)` 触发执行。

---

### 10.6 常见陷阱：误导入同名内置模块

如果你的代码里有：

```python
from autorigs import mainGUI     # ← 这导入的是 Houdini 官方骨骼绑定工具，不是你自己的文件！
```

`autorigs` 是 Houdini 内置的自动骨骼绑定工具包，`mainGUI.MainGUI()` 会创建官方的骨骼绑定界面（显示 Spine / Head / Arm / Leg 等按钮），而不是你自己定义的类。

**正确做法：** 直接在 `.pypanel` 脚本中定义类，或导入你自己的模块文件：

```python
# ✅ 正确：直接使用本文件定义的类
def onCreateInterface():
    widget = MainGUI()           
    widget.buildMainGUI(kwargs["paneTab"])
    return widget

# ❌ 错误：from autorigs import mainGUI 会导入 Houdini 官方模块
```

---

## 十一、完整项目结构总览

```
$HOUDINI_USER_PREF_DIR/
├── python_panels/
│   └── my_panel.pypanel          ← 在 Python Panel Editor 中创建和编辑
└── scripts/
    └── python/
        └── my_panel_gui.py       ← 界面逻辑主文件，包含 MyPanelGUI 类
```

---

> **小结：** 创建 Houdini Python Panel 的核心流程是：
>
> 1. 用 PySide6 编写继承自 `QWidget` 的界面类
> 2. 在 `.pypanel` 脚本中实现 `onCreateInterface()`（返回 QWidget）和 `onDestroyInterface()`
> 3. 在 Python Panel Editor 中配置并勾选菜单选项
> 4. 通过面板 + 号菜单嵌入使用
> 5. 按需添加 `hou` API 调用、事件回调实现自定义功能

