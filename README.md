# U1_Filament_cutter
Integrated mechanical filament cutter into toolhead and Klipper macros for the Snapmaker U1, enabling reliable multi-material (MMU) printing.

# U1_Filament_cutter: Active Toolhead Filament Cutter for Snapmaker U1

Project **U1_Filament_cutter** brings a highly reliable, filament cutting mechanism directly to the Snapmaker U1 toolhead. Designed specifically to unlock flawless automated multi-color and multi-material printing when paired with external MMU systems (like the Anycubic ACE Pro 1/2 via the [MultiACE](https://github.com/decay71/multiACE) project).

Relying on traditional pull-back retractions and tip forming for automated filament swaps often leads to stringing, clogging, and failed prints. By cutting the filament cleanly right above the hotend, **U1_Filament_cutter** ensures every filament swap is perfect, requiring less time than tip-forming.

---

## 🌟 Key Features

* **Passive Actuation:** Zero electronics added to the toolhead. The cutter is purely mechanical, triggered by a precisely calculated kinematic collision with a frame-mounted block.
* **Low Print Area Loss:** By defining a tiny "Bed Exclude Area", around 10x35mm, in your slicer, the reaction block sits safely in the rear-left corner (Y270) without sacrificing usable print volume.
* **Firmware Integration:** Custom Klipper macros to seamlessly integrate directly with the MultiACE or other MMU system toolchange sequence.

## ⚠️ Disclaimer
**Use at your own risk.** This hardware and software modification is provided "as is", with no warranty of any kind. By proceeding, you accept full responsibility for any damage to your printer or other equipment (including voided warranties), as well as any personal injury, cuts, burns, or property/bodily harm to yourself or others during assembly, calibration, or use. The author assumes no liability for direct or indirect damages. If you do not agree, do not proceed.

---

## 📋 Prerequisites

1. **Snapmaker U1** with root/SSH access enabled.
2. **Optional** An external filament manager (e.g., Anycubic ACE Pro 1/2) managed by [MultiACE](https://github.com/decay71/multiACE) .
3. Printed parts from the `/3D_Models/` folder (PETG-CF recommended).
   * 1x Backplate
   * 1x Blade holder
   * 1x Gear unlock lever
   * 1x Cutter lever (3D printed option not advised)
   * 1x Cut block
   * 1x Cut block nut
4. Hardware BOM:
   * 1x Cutter lever machined by alluminium or other harder material than 3d printed part
   * 1x STANLEY 11-300 blade segment.
   * 1x Torsion spring (see `Docs/Torsion_spring.md` for specs).
   * M3 screws x 5mm.

---

## 🛠️ Hardware Installation in short (Please refer to the [Assembly Instructions](https://github.com/cuschiec/U1_Filament_cutter/blob/main/Docs/U1-Cut%20Modification%20Assembly%20Instructions.pdf))

1. **Toolhead Modification:** Assemble the cutter lever, gear release lever, spring, blade together on the Backplate. Mount it to the U1 toolhead. Ensure the lever moves smoothly and springs back fully. (to be integrated by assembly guide)
2. **Frame modification:** Mount the printed passive Cut block to the rear-left part of the frame. Part of side cover and top black frame have to be cut (to be integrated by assembly guide)


---

## 💻 Software & Firmware Configuration in short (Please refer to the [Assembly Instructions](https://github.com/cuschiec/U1_Filament_cutter/blob/main/Docs/U1-Cut%20Modification%20Assembly%20Instructions.pdf))
### 1. Snapmaker ORCA (Bed Exclude Area)
To prevent the printer from moving into the reaction block during normal printing, add a bed exclude area in Snapmaker Orca slicer.
* Exclude a rectangle of **10mm (X) by 40mm (Y)** in the rear-left corner (`X0 Y270`).
* (to be integrated by SO setting image)


### 2. Klipper Configuration
In your `printer.cfg`, include the Macro.cfg file, write at the end of file:
[include Macro.cfg] 

Copy Macro.cfg file into the root of your printer (use Fluidd to add the file)


### 3. MultiACE Integration (thx to Decay71)
(under development)
Two parameter were added to the MultiACE software (from v1.30) to test it:
* unload_cut_macro      gcode macro that cuts the filament at the toolhead
                        (cutter mod), called instead of the tip forming,
                        gets HEAD=n; background unloads then run inline
  
* unload_cut_heads      heads with a cutter, e.g. 1,2 (empty = all), the
                        others keep the normal tip forming




### 4. Testing

Test Filament Cut: 
*Load the modified toolhead. 
*Load filament from ACE (MultiACE page). 
*Jog the toolhead in front of the Cut block (around X7.5 Y269.9), adjust the Y position to be in the middle of the cutting lever and the X position to have the cutting block 0.2mm from the cutter lever (use a sheet of paper as reference of 0.2mm).
*Note down the X-Y coordinate from Fluidd interface and update the CUT macro parameter then restart the printer.
*Unload filament by MultiACE page.
*The toolhead should reach setpoint temperature, travel safely to the X/Y alignment coordinate, retract filament to have softer filament, push to the reaction to actuate the lever three time, and retract again out of the extruding gear at this point MultiACE will Rollback filament.


## 🤝 Contributing
Contributions, issues, and feature requests are welcome! Feel free to check the issues page.


## License

This project is dual-licensed to best protect both the software and hardware components:

* **Software & Firmware Macros:** Licensed under the [GNU General Public License v3.0](LICENSE).
* **Hardware & 3D Models:** Licensed under the [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License](LICENSE-HARDWARE.md) (CC BY-NC-SA 4.0).
