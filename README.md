# VOBOT D1 Python App Builder Skill

A Claude Code skill for building Python mini-apps that run on **VOBOT D1 / Mini Dock**.

It helps Claude generate VOBOT-ready app folders with the correct:
- `__init__.py` entrypoint
- `resources/` layout
- `NAME` constant usage
- resource paths such as `A:apps/{NAME}/resources/...`
- deployment steps for **Developer Mode + Thonny**

## What this skill does

This skill guides Claude to:
- create new VOBOT D1 Python apps
- modify existing VOBOT D1 apps
- generate minimal LVGL demos
- keep resource paths compatible with VOBOT's app folder renaming behavior
- provide deployment and debugging steps

## Important VOBOT D1 rule

The app entry file should be:

```text
__init__.py
```

Not `main.py`.

A typical app generated with this skill looks like:

```text
my-app/
├── __init__.py
└── resources/
    ├── icon.png
    └── other-assets...
```

## Install the skill locally

Clone this repo or download it, then link it into Claude Code's local skills directory:

```bash
ln -s "/path/to/vobot-d1-skill" "$HOME/.claude/skills/vobot-d1-python-app-builder"
```

Then reload Claude Code plugins or restart Claude Code.

## Official references

- VOBOT developer getting started: https://dock.myvobot.com/developer/getting_started/
- Important resource path configuration: https://dock.myvobot.com/developer/getting_started/#important-resource-file-path-configuration
- Thonny download: https://thonny.org/

## Trigger examples

After installation, ask Claude things like:

```text
Make a hello world app for VOBOT D1
```

```text
Make a hello world app for VOBOT D1
```

```text
Please help me write an application that can display QR codes on a VOBOT D1.
```

```text
Please make me a VOBOT D1 Tetris game.
```

## Example output style

This skill encourages Claude to return:

1. app directory tree
2. file contents
3. deployment steps
4. debugging tips

Example generated structure:

```text
qr-display/
├── __init__.py
└── resources/
```

Example code pattern:

```python
import lvgl as lv

NAME = "Hello World"
scr = None
label = None

async def on_start():
    global scr, label
    scr = lv.obj()
    label = lv.label(scr)
    label.set_text("Hello World")
    label.center()
    lv.scr_load(scr)

async def on_running_foreground():
    return

async def on_stop():
    global scr
    if scr is not None:
        scr.clean()
        scr = None
```

## Deployment workflow

Typical deployment steps:

1. Enable **Developer Mode** on the device
2. Power-cycle the device
3. Connect it by USB
4. Open **Thonny**
5. Select the ESP32 connection
6. Open the Files view
7. Copy the app folder into the device `apps` directory
8. Press `Ctrl+D`

## Resource path rule

VOBOT renames the app folder to match the app `NAME`.

Because of that, resource paths should use:

```python
f"A:apps/{NAME}/resources/icon.png"
```

Do not rely on the local temporary folder name.

## Repository contents

- `SKILL.md` — main Claude Code skill definition
- `references/vobot-d1-getting-started.md` — condensed VOBOT D1 setup and deployment reference

## Notes

This repository contains the **skill**, not a standalone VOBOT firmware package.
It is intended to improve Claude Code's output when generating VOBOT D1 apps.
