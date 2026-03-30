---
name: vobot-d1-python-app-builder
description: This skill should be used when the user asks to build a VOBOT D1 Python mini app, create a VOBOT D1 application, write a Python program that runs on VOBOT D1, create a VOBOT D1 mini app, deploy a Python app to VOBOT D1, or needs help with VOBOT D1 developer mode, Thonny setup, app folder structure, resource paths, upload, restart, or debugging.
version: 0.1.0
---

# VOBOT D1 Python App Builder

Use the following workflow whenever you generate, modify, or review a runnable Python mini app for VOBOT D1.

## Goal

Turn the user's request into a Python app folder that can be copied directly into the VOBOT D1 `apps` directory.

Prioritize:
- a minimal runnable app
- a clear directory structure
- correct resource paths
- a file set that can be uploaded directly to the device
- short deployment and debugging steps

## Confirm The Inputs First

Before writing code, confirm these details from the user's request:
- app name
- what the app should do
- whether it needs a UI
- whether it needs images, icons, fonts, audio, or other assets
- whether it needs networking, sensors, timers, or background logic
- whether the user wants a minimal demo or a fuller app

If the request is incomplete, ask the 2-3 most important follow-up questions before generating code.

## Rules You Must Follow

### 1. Use A Dedicated App Folder

Put each app in its own folder and place related assets inside a `resources/` subdirectory.

Recommended structure:

```text
<app-folder>/
├── __init__.py
└── resources/
    ├── icon.png
    └── ...
```

If the user does not specify a filename, write the main program to `__init__.py`. This is the required entrypoint filename for VOBOT D1 app recognition.

Never generate the entry file as `main.py`. On VOBOT D1, that prevents the app from being recognized correctly.

### 2. Always Declare The App Name Constant

Keep an app name constant in the code:

```python
NAME = "My App"
```

Treat it as the single source of truth for resource path construction.

### 3. Resource Paths Must Be Based On NAME

The VOBOT platform renames the app folder to match `NAME`.

Because of that, resource file paths must never depend on a temporary local folder name. Always use device-style paths:

```python
f"A:apps/{NAME}/resources/icon.png"
```

Do not use:
- relative local paths that depend on the current working directory
- hard-coded original folder names
- absolute desktop paths that break before or after upload

This is the most important constraint. If the app includes assets, check this first.

### 4. Prefer A Minimal LVGL UI

If the user wants a UI but does not request a complex layout, default to a minimal LVGL screen:
- create the screen with `lv.obj()`
- create text with `lv.label(scr)`
- center the label with `label.center()`
- load the screen with `lv.scr_load(scr)`

### 5. Use The Lifecycle Function Pattern From The Reference

Prefer the async lifecycle pattern shown in the reference:
- `async def on_start()`
- `async def on_running_foreground()`
- `async def on_stop()`

Usage conventions:
- `on_start()`: initialize UI, state, and resources
- `on_running_foreground()`: run periodic foreground update logic
- `on_stop()`: clean up the screen, resources, and state

If the app is only a static screen, it is fine to keep most logic inside `on_start()`.

## Default Implementation Template

For a minimal demo, prefer a structure like this:

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

Adapt the text, state, and interaction to the user's needs, but keep this lifecycle skeleton unless the user clearly asks for a different structure.

## Code Generation Workflow

### App Without Assets

1. Create the minimal directory.
2. Write the main program.
3. Organize logic with `on_start/on_stop`.
4. Provide the upload location and restart step.

### App With Assets

1. List the required asset files first.
2. Put all assets under `resources/`.
3. Make every resource path in code derive from `NAME`.
4. Recheck for hard-coded folder names.
5. Provide the full directory tree.

### Modifying An Existing App

1. Find the `NAME` definition.
2. Find every resource path.
3. Confirm they follow `A:apps/{NAME}/resources/...`.
4. Then update the feature logic.

## Deployment Steps

When handing off an app, include a short deployment guide by default:

1. Enable Developer Mode on the device.
2. Power-cycle the device.
3. Connect it to the computer with a USB cable.
4. Open Thonny.
5. Select the ESP32 connection.
6. Open the Files view.
7. Copy the app folder into the device `apps` directory.
8. After uploading or deleting an app, press `Ctrl+D` to restart or resume execution.

If the user only wants code, keep the deployment instructions as short as possible.

## Debugging Steps

If the app has runtime issues, check these first:
- whether Developer Mode is enabled
- whether the device was power-cycled after enabling it
- whether the correct ESP32 connection was selected
- whether the app folder was copied under `apps`
- whether `NAME` matches the app's final folder name
- whether resource paths use `A:apps/{NAME}/resources/...`
- whether `Ctrl+D` was pressed after upload
- whether Thonny shows device-side errors in the log

Once developer mode is connected, direct the user to Thonny logs first when diagnosing errors.

## Recommended Output Format

When presenting results to the user, prefer this order:

1. app directory tree
2. contents of each file
3. upload steps
4. debugging notes

If there are many files, provide the directory tree first, then output files one by one.

## When To Read The Reference

Read `references/vobot-d1-getting-started.md` first when:
- you need to verify device deployment steps
- you need to confirm the resource path rule
- you need to double-check the lifecycle example
- you need to explain Developer Mode or the Thonny connection flow

## Errors To Avoid

Do not:
- assume assets can be loaded from local desktop paths
- place assets outside `resources/` while still referencing them as bundled resources
- delete the `apps` directory itself; only remove the target app folder when uninstalling
- generate an overly complex project when the user only asked for a minimal demo
- ignore consistency between `NAME` and resource paths

## Success Criteria

The generated result should satisfy all of these:
- the folder can be copied directly into the device `apps` directory
- the main code structure is clear
- the resource paths are valid on the device
- the user can upload the app and inspect logs with Thonny
- for a minimal demo, the code stays as short as possible while still running
