# U1_Filament_cutter
Integrated mechanical filament cutter into toolhead and Klipper macros for the Snapmaker U1, enabling reliable multi-material (MMU) printing.

# U1-Cut: Active Toolhead Filament Cutter for Snapmaker U1

Project **U1-Cut** brings a highly reliable, Bambu-style active filament cutting mechanism directly to the Snapmaker U1 toolhead. Designed specifically to unlock flawless automated multi-color and multi-material printing when paired with external MMU systems (like the Anycubic ACE Pro via the [MultiACE](https://github.com/decay71/multiACE) project).

Relying on traditional pull-back retractions for automated filament swaps often leads to stringing, clogging, and failed prints. By cutting the filament cleanly right above the hotend, **U1-Cut** ensures every filament swap is perfect, requiring zero tip-forming time.

---

## 🌟 Key Features

* **Passive Actuation:** Zero electronics added to the toolhead. The cutter is purely mechanical, triggered by a precisely calculated kinematic collision with a frame-mounted block.
* **Zero Print Area Loss:** By defining a tiny 10x50mm "Bed Exclude Area" in your slicer, the reaction block sits safely in the front-left corner without sacrificing usable print volume.
* **Firmware Integration:** Custom Klipper macros and Python patches to seamlessly override homing routines and integrate directly with the MultiACE toolchange sequence.

## ⚠️ Disclaimer
**Use at your own risk.** This modification requires root access to your Snapmaker U1 and modifies core Klipper configuration files (`printer.cfg` and Python modules). Incorrect homing configurations can cause the toolhead to crash into the frame. Please read the instructions carefully.

---

## 📋 Prerequisites

1. **Snapmaker U1** with root/SSH access enabled.
2. An external filament manager (e.g., Anycubic ACE Pro) running the [MultiACE](https://github.com/decay71/multiACE) backend.
3. Printed parts from the `/3D_Models/STLs` folder (PETG, ABS, or ASA recommended).
4. Hardware BOM:
   * 1x Standard utility knife blade segment.
   * 1x Compression spring (see `Docs/BOM.md` for specs).
   * M3 screws/heat-set inserts for mounting.

---

## 🛠️ Hardware Installation

1. **Toolhead Modification:** Assemble the printed cutter lever, spring, and blade. Mount it to the designated points on the U1 toolhead. Ensure the lever moves smoothly and springs back fully.
2. **Reaction Block:** Mount the printed passive reaction block to the front-left frame (Position `X=0, Y=0 to Y=50`). 

*Please refer to `/Docs/Assembly_Guide.pdf` (or images) for detailed mechanical assembly.*

---

## 💻 Software & Firmware Configuration

### 1. Slicer Setup (Bed Exclude Area)
To prevent the printer from moving into the reaction block during normal printing, add a bed exclude area in your slicer (OrcaSlicer / PrusaSlicer).
* Exclude a rectangle of **10mm (X) by 50mm (Y)** in the front-left corner (`X0 Y0`).

### 2. Klipper Configuration (`printer.cfg`)
You must invert the X-axis homing direction to target the right side of the frame (`X=271`) to avoid crashing into the newly installed reaction block during startup.

In your `printer.cfg`, update the `[stepper_x]` section:
```ini
[stepper_x]
position_endstop: 271        # Moved from 0 to 271
position_max: 271
homing_positive_dir: true    # Added to force homing to the right

Note: The native Snapmaker _HOMING_PRECISE_COREXY_ADVANCED macro will automatically adapt and perform the diagonal squaring bump on the right side.

Add the CUT_FILAMENT macro provided in /Software/Macros/CUT_FILAMENT.cfg to your configuration.

3. MultiACE Integration
To bypass the stock tip-forming and integrate the mechanical cut, you need to apply two patches:

Python Patch (ace_swap.py): Inject the CUT_FILAMENT call right before the unload sequence is delegated (specifically for Tool 3 / the MMU tool). See /Software/Firmware_Patches/ace_swap_patch.md for the exact line changes.

Tipform Bypass (ace.cfg): Disable standard tip-forming by adding the custom table to your MultiACE config:

🧪 Calibration & Testing
Dry Run Homing: Hand-position the toolhead in the center of the bed. Send G28 X and keep your hand on the power switch. Ensure the toolhead homes to the right.

Dry Run Cut: Select the MMU tool (e.g., T3). Send the CUT_FILAMENT command. The toolhead should perform a Z-hop, travel safely to the Y-alignment coordinate, crash into the X0 block to actuate the lever, and immediately retract.

Live Test: Load a filament manually, ensure the hotend is at temperature, and run CUT_FILAMENT. The filament should be cleanly severed.

🤝 Contributing
Contributions, issues, and feature requests are welcome! Feel free to check the issues page.


## License

This project is dual-licensed to best protect both the software and hardware components:

* **Software & Firmware Macros:** Licensed under the [GNU General Public License v3.0](LICENSE).
* **Hardware & 3D Models:** Licensed under the [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License](LICENSE-HARDWARE.md) (CC BY-NC-SA 4.0).
