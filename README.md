# U1_Filament_cutter
Integrated mechanical filament cutter into toolhead and Klipper macros for the Snapmaker U1, enabling reliable multi-material (MMU) printing.

# U1_Filament_cutter: Active Toolhead Filament Cutter for Snapmaker U1

Project **U1_Filament_cutter** brings a highly reliable, filament cutting mechanism directly to the Snapmaker U1 toolhead. Designed specifically to unlock flawless automated multi-color and multi-material printing when paired with external MMU systems (like the Anycubic ACE Pro 1/2 via the [MultiACE](https://github.com/decay71/multiACE) project).

Relying on traditional pull-back retractions and tip forming for automated filament swaps often leads to stringing, clogging, and failed prints. By cutting the filament cleanly right above the hotend, **U1_Filament_cutter** ensures every filament swap is perfect, requiring zero tip-forming time.

---

## 🌟 Key Features

* **Passive Actuation:** Zero electronics added to the toolhead. The cutter is purely mechanical, triggered by a precisely calculated kinematic collision with a frame-mounted block.
* **Low Print Area Loss:** By defining a tiny "Bed Exclude Area", around 10x40mm, in your slicer, the reaction block sits safely in the front-left corner without sacrificing usable print volume.
* **Firmware Integration:** Custom Klipper macros and Python patches to seamlessly override homing routines and integrate directly with the MultiACE toolchange sequence.

## ⚠️ Disclaimer
**Use at your own risk.** This modification requires root access to your Snapmaker U1 and modifies core Klipper configuration files (`printer.cfg` and Python modules). Incorrect homing configurations can cause the toolhead to crash into the frame. Please read the instructions carefully.

---

## 📋 Prerequisites

1. **Snapmaker U1** with root/SSH access enabled.
2. **Optional** An external filament manager (e.g., Anycubic ACE Pro 1/2) managed by [MultiACE](https://github.com/decay71/multiACE) .
3. Printed parts from the `/3D_Models/` folder (PETG-CF recommended).
   * 1x Backplate
   * 1x Blade holder
   * 1x Blade lock
   * 1x Gear unlock lever
4. Hardware BOM:
   * 1x Cutter lever machined by alluminium or other harder material than 3d printed part
   * 1x Standard utility knife blade segment.
   * 1x Torsion spring (see `Docs/BOM.md` for specs).
   * M3 screws x 8mm.

---

## 🛠️ Hardware Installation

1. **Toolhead Modification:** Assemble the printed cutter lever, spring, and blade. Mount it to the designated points on the U1 toolhead. Ensure the lever moves smoothly and springs back fully. (to be integrated by assembly guide)
2. **Reaction Block:** Mount the printed passive reaction block to the front-left frame. (to be integrated by assembly guide)

*Please refer to `/Docs/Assembly_Guide.pdf` (or images) for detailed mechanical assembly.*

---

## 💻 Software & Firmware Configuration
### 1. Slicer Setup (Bed Exclude Area)
To prevent the printer from moving into the reaction block during normal printing, add a bed exclude area in Snapmaker Orca slicer.
* Exclude a rectangle of **10mm (X) by 50mm (Y)** in the front-left corner (`X0 Y0`).
* (to be integrated by SO setting image)


### 2. Klipper Configuration (`printer.cfg`)
You must invert the X-axis homing direction to target the right side of the frame (`X=271`) to avoid crashing into the newly installed reaction block during startup.

In your `printer.cfg`, update the `[stepper_x]` section:

[stepper_x]
position_endstop: 271        # Moved from 0 to 271
position_max: 271
homing_positive_dir: true    # Added to force homing to the right

Note: The native Snapmaker _HOMING_PRECISE_COREXY_ADVANCED macro will automatically adapt and perform the diagonal squaring bump on the right side.

Add the CUT_FILAMENT macro provided in /Software/CUT_FILAMENT.cfg to your printer.cfg.


### 3. MultiACE Integration
(under development)


### 4. Testing
Test Homing: Send G28 X and keep your hand on the power switch. Ensure the toolhead homes to the right.

Test Filament Cut: Load the modified toolhead. Send the CUT_FILAMENT command in fluidd terminal. The toolhead should reach setpoint temperature, perform a Z-hop, travel safely to the X/Y alignment coordinate, retract filament to have softer filament, push to the reaction to actuate the lever two time, and immediately retract. Disconnect PTFE from top of the toolhead to check the filament cut


## 🤝 Contributing
Contributions, issues, and feature requests are welcome! Feel free to check the issues page.


## License

This project is dual-licensed to best protect both the software and hardware components:

* **Software & Firmware Macros:** Licensed under the [GNU General Public License v3.0](LICENSE).
* **Hardware & 3D Models:** Licensed under the [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License](LICENSE-HARDWARE.md) (CC BY-NC-SA 4.0).
