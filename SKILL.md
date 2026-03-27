---
name: vobot-d1-python-app-builder
description: This skill should be used when the user asks to "开发 VOBOT D1 Python 小程序", "制作 VOBOT D1 应用", "编写运行在 VOBOT D1 上的 Python 程序", "创建 VOBOT D1 mini app", "部署 Python app 到 VOBOT D1", or needs help with VOBOT D1 developer mode, Thonny setup, app folder structure, resources paths, upload, restart, or debugging.
version: 0.1.0
---

# VOBOT D1 Python App Builder

为 VOBOT D1 生成、修改、检查可运行的 Python 小程序时，按以下流程执行。

## 目标

将用户需求转成一个可直接放入 VOBOT D1 `apps` 目录的 Python 应用目录。

优先产出：
- 最小可运行应用
- 明确的目录结构
- 正确的资源路径
- 可直接上传到设备的文件集合
- 简短的部署与调试步骤

## 先确认输入

开始编码前，先从用户需求中确认这些信息：
- 应用名称
- 应用要做什么
- 是否需要界面
- 是否需要图片、图标、字体、音频等资源
- 是否需要联网、传感器、定时器或后台逻辑
- 是否只要最小 demo，还是要完整应用

如果需求不完整，先补齐最关键的 2-3 个问题，再开始生成代码。

## 生成应用时必须遵守的规则

### 1. 使用独立应用目录

将每个应用放在自己的文件夹中，并把相关资源放进 `resources/` 子目录。

推荐结构：

```text
<app-folder>/
├── __init__.py
└── resources/
    ├── icon.png
    └── ...
```

如果用户没有指定文件名，默认把主程序写成 `__init__.py`。这是 VOBOT D1 应用识别所需的入口文件名。

绝不要把入口文件生成成 `main.py`。对于 VOBOT D1，这会导致应用无法被正确识别。

### 2. 始终声明应用名常量

在代码中保留应用名常量：

```python
NAME = "My App"
```

把它当作资源路径拼接的唯一来源。

### 3. 资源路径必须基于 NAME

VOBOT 平台会把应用文件夹重命名成与 `NAME` 一致的名字。

因此，任何资源文件路径都不要依赖本地临时文件夹名。始终使用设备路径格式：

```python
f"A:apps/{NAME}/resources/icon.png"
```

不要写成：
- 相对本地路径依赖当前工作目录
- 硬编码的原始文件夹名
- 上传前后会失效的绝对桌面路径

这是最重要的约束。如果应用包含资源，优先检查这一点。

### 4. 优先生成最小 LVGL 界面

如果用户需要界面但没有指定复杂布局，默认使用最小 LVGL 页面：
- `lv.obj()` 创建 screen
- `lv.label(scr)` 创建文本
- `label.center()` 居中
- `lv.scr_load(scr)` 加载页面

### 5. 使用页面给出的生命周期函数模式

优先采用页面展示的异步生命周期：
- `async def on_start()`
- `async def on_running_foreground()`
- `async def on_stop()`

用途约定：
- `on_start()`：初始化界面、状态、资源
- `on_running_foreground()`：前台周期更新逻辑
- `on_stop()`：清理 screen、资源、状态

如果只是静态页面，可以把主要逻辑放在 `on_start()`。

## 默认实现模板

生成最小 demo 时，优先遵循这类结构：

```python
import lvgl as lv

NAME = "Hello World"
scr = None
label = None
counter = 0

async def on_start():
    global scr, label
    print('on start')
    scr = lv.obj()
    label = lv.label(scr)
    label.set_text('Hello World')
    label.center()
    lv.scr_load(scr)

async def on_running_foreground():
    global counter
    counter += 1
    label.set_text('Hello World ' + str(counter))

async def on_stop():
    global scr
    print('on stop')
    if scr is not None:
        scr.clean()
        del scr
```

按用户需求改写文案、状态与交互，但尽量保留这套生命周期骨架，除非用户明确要求别的结构。

## 生成代码时的工作流

### 无资源应用

1. 生成最小目录
2. 写主程序
3. 使用 `on_start/on_stop` 组织逻辑
4. 给出上传位置与重启步骤

### 有资源应用

1. 先列出所需资源文件
2. 把资源统一放入 `resources/`
3. 代码中所有资源路径都基于 `NAME`
4. 再次检查是否出现硬编码目录名
5. 给出完整目录树

### 修改现有应用

1. 找到 `NAME` 定义
2. 找到所有资源路径
3. 检查是否符合 `A:apps/{NAME}/resources/...`
4. 再修改功能逻辑

## 部署步骤

向用户交付应用时，默认附上简短部署说明：

1. 在设备中开启 Developer Mode
2. 断电重连设备
3. 用 USB 数据线连接电脑
4. 打开 Thonny
5. 选择 ESP32 连接
6. 打开 Files 视图
7. 把应用文件夹复制到设备的 `apps` 目录
8. 上传或删除应用后，按 `Ctrl+D` 重启/继续执行

如果用户只要代码，不额外展开背景说明，只保留最短可执行步骤。

## 调试步骤

出现运行问题时，优先检查：
- 是否已经开启 Developer Mode
- 是否重新断电连接
- 是否选择了正确的 ESP32 连接
- 是否把应用目录放到了 `apps` 下
- `NAME` 是否与应用最终目录名一致
- 资源路径是否写成 `A:apps/{NAME}/resources/...`
- 上传后是否执行了 `Ctrl+D`
- Thonny 日志里是否有设备报错

开发模式连接后，优先让用户查看 Thonny 输出日志定位错误。

## 输出格式建议

为用户生成结果时，优先按这个顺序输出：

1. 应用目录树
2. 每个文件内容
3. 上传步骤
4. 调试提示

如果文件较多，先给目录树，再分文件输出。

## 何时需要参考资料

遇到以下情况时，先读取 `references/vobot-d1-getting-started.md`：
- 需要核对设备部署步骤
- 需要确认资源路径规则
- 需要复核生命周期示例
- 需要解释 Developer Mode / Thonny 连接方式

## 明确避免的错误

不要做这些事：
- 不要假设资源可以用本地桌面路径读取
- 不要把资源放在 `resources/` 之外却仍按资源文件引用
- 不要删除 `apps` 目录本身；卸载时只删除目标应用文件夹
- 不要在用户只要最小 demo 时生成过度复杂的工程
- 不要忽略 `NAME` 与资源路径的一致性

## 成功标准

生成结果应满足：
- 目录可直接复制到设备 `apps` 目录
- 主代码结构清晰
- 资源路径可在设备上成立
- 用户能用 Thonny 完成上传和查看日志
- 对于最小 demo，代码尽量短且能跑
