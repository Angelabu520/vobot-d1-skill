# VOBOT D1 Getting Started Reference

本参考整理自 VOBOT D1 developer getting started 页面，用于在生成应用、部署应用、排查问题时快速查阅。

## 1. 开发前提

需要：
- Mini Dock / VOBOT D1 设备
- Windows / macOS / Linux 电脑
- USB-C 数据线
- Thonny

## 2. 设备准备

1. 在设备设置中开启 **Developer Mode**。
2. 开启后，需要给设备断电并重新连接电源，确保开发模式生效。
3. 用 USB 数据线把设备连接到电脑。
4. 在 Thonny 里选择 ESP32 连接。
5. 打开 Files 视图管理板端文件。

## 3. 应用目录规则

每个 app 使用独立文件夹。

推荐结构：

```text
my-app/
├── __init__.py
└── resources/
    ├── icon.png
    └── other-assets...
```

资源统一放进 `resources/`。

## 4. 最重要的路径约束

平台会把应用目录重命名为 `NAME` 变量对应的名字。

因此资源路径必须使用这种设备路径：

```python
f"A:apps/{NAME}/resources/icon.png"
```

也可以抽象成：

```text
A:apps/{APP NAME}/resources/...
```

不要依赖本地生成时的目录名，否则上传到设备后资源引用可能失效。

## 5. 页面展示的示例代码要点

页面中可见的关键代码片段包括：

```python
import lvgl as lv

NAME = "Hello World"
scr = None
label = None
counter = 0

async def on_running_foreground():
    global counter
    counter += 1
    label.set_text('Hello World ' + str(counter))

async def on_stop():
    global scr
    print('on stop')
    scr.clean()
    del scr

async def on_start():
    global scr, label
    print('on start')
    scr = lv.obj()
    label = lv.label(scr)
    label.set_text('Hello World')
    label.center()
    lv.scr_load(scr)
```

说明：
- `on_start()`：初始化页面
- `on_running_foreground()`：前台运行时周期执行，页面说明里提到大约每 200ms 触发一次
- `on_stop()`：停止时清理资源

## 6. 部署方式

没有单独的打包步骤。

安装 app 的方式就是：
- 在 Thonny 的板端文件视图中
- 把整个 app 文件夹复制到设备的 `apps` 目录

## 7. 卸载方式

卸载时：
- 只删除目标 app 的文件夹
- 不要删除 `apps` 目录本身

## 8. 上传后重启

上传或删除 app 后，按：

```text
Ctrl+D
```

用于重启/继续执行设备程序。

## 9. 调试

连接开发模式后，可以在 Thonny 中查看日志。

页面说明中提到：
- 设备侧错误会打印出来
- 可以借助日志定位问题
- Thonny 还可以查看 flash 使用情况和剩余空间

## 10. 生成代码时的检查清单

每次产出 VOBOT D1 app 前后，检查：
- 是否定义了 `NAME`
- 是否使用了 `__init__.py`
- 是否把资源放进 `resources/`
- 是否所有资源路径都基于 `A:apps/{NAME}/resources/...`
- 是否给出了上传到 `apps` 的说明
- 是否提醒了 `Ctrl+D`
- 是否把调试入口指向 Thonny 日志
