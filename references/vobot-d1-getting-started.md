# VOBOT D1 Getting Started Reference

This reference condenses the VOBOT D1 developer getting started page so it can be used quickly while generating apps, deploying them, and troubleshooting issues.

## 1. Prerequisites

You need:
- a Mini Dock / VOBOT D1 device
- a Windows, macOS, or Linux computer
- a USB-C data cable
- Thonny

## 2. Device Setup

1. Enable **Developer Mode** in the device settings.
2. After enabling it, power-cycle the device so developer mode takes effect.
3. Connect the device to the computer with a USB cable.
4. In Thonny, select the ESP32 connection.
5. Open the Files view to manage files on the device.

## 3. App Directory Rules

Each app should use its own folder.

Recommended structure:

```text
my-app/
├── __init__.py
└── resources/
    ├── icon.png
    └── other-assets...
```

Place all bundled assets inside `resources/`.

## 4. Most Important Path Constraint

The platform renames the app directory to match the value of `NAME`.

Because of that, asset paths must use the device path format:

```python
f"A:apps/{NAME}/resources/icon.png"
```

You can generalize it as:

```text
A:apps/{APP NAME}/resources/...
```

Do not rely on the local temporary folder name used during generation, or asset references may break after upload.

## 5. Key Code Pattern From The Reference

The page shows core code like this:

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

Notes:
- `on_start()`: initialize the screen
- `on_running_foreground()`: run periodically in the foreground; the page notes it is triggered about every 200 ms
- `on_stop()`: clean up resources when the app stops

## 6. Deployment Method

There is no separate packaging step.

To install an app:
- use the board-side Files view in Thonny
- copy the entire app folder into the device `apps` directory

## 7. Uninstall Method

When uninstalling:
- delete only the target app folder
- do not delete the `apps` directory itself

## 8. Restart After Upload

After uploading or deleting an app, press:

```text
Ctrl+D
```

This restarts or resumes program execution on the device.

## 9. Debugging

After connecting in developer mode, you can inspect logs in Thonny.

The page notes that:
- device-side errors are printed to the log
- logs can be used to locate problems
- Thonny can also show flash usage and remaining space

## 10. Checklist For Generated Code

Before and after generating a VOBOT D1 app, check:
- whether `NAME` is defined
- whether the entry file is `__init__.py`
- whether assets are placed under `resources/`
- whether all asset paths are based on `A:apps/{NAME}/resources/...`
- whether upload instructions mention the `apps` directory
- whether the user is reminded to press `Ctrl+D`
- whether debugging points the user to Thonny logs
