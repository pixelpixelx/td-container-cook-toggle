# TouchDesigner – Container Cook Toggle (Evaluation Control)

This repository contains a small, reusable TouchDesigner utility network for toggling `allowCooking` on a Container COMP using a Button CHOP and a CHOP Execute DAT.

Disabling cooking halts **all evaluation** inside the container, including CHOPs, TOPs, GLSL shaders, feedback loops, Python callbacks, and time-based processes. As a result, this tool preserves **both CPU and GPU resources**, making it well suited for large-scale projects and live visual performance workflows.

---

## Intended Workflow (Important)

This system is designed to be:
- Duplicated per container
- Renamed per container
- Collected into a single control panel interface

Each toggle controls **one specific container**. By creating multiple instances, you can manage the cooking state of many subsystems from one place without navigating deep into networks.

---

## What It Does

- Button ON → target container cooks (evaluates normally)
- Button OFF → target container stops cooking (evaluation is frozen)

Stopping cooking prevents:
- CPU evaluation of CHOPs and DATs
- GPU evaluation of TOPs and GLSL shaders
- Texture updates and feedback loops
- Time-based advancement and callbacks

This allows entire subsystems to be paused safely without disconnecting operators or breaking internal structure.

---

## Network Overview

- `cookToggle_btn` — Button CHOP (user-facing control)
- `cookToggle_out` — Normalized state output
- `cookToggle_exec` — CHOP Execute DAT (logic)
- `container1` — **Target Container COMP whose cooking is being controlled**

---

## How It Works

The CHOP Execute DAT listens for value changes from the Button CHOP and toggles cooking on the target container:

```python
def onValueChange(channel, sampleIndex, val, prev):
    parent().allowCooking = bool(val)
    return
