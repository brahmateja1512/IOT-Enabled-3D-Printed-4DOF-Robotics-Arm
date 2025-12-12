## 🛠️ 3D Printing Procedure

This section details the manufacturing process for the 4DOF Robotic Arm using FDM 3D printing. All `.stl` files can be found in the `/STL_Files` directory of this repository.

### 1. Prerequisites
* **3D Printer:** Minimum build volume of $200 \times 200 \times 200$ mm (e.g., Ender 3, Prusa i3).
* **Material:** PLA+ or PETG is recommended.
    * *Note: Standard PLA may be too brittle for the high-torque joints.*
* **Slicer Software:** Cura, PrusaSlicer, or Simplify3D.

### 2. Slicing Configuration
To ensure mechanical rigidity without wasting material, apply the following settings in your slicer.

| Setting | Value | Reason |
| :--- | :--- | :--- |
| **Layer Height** | 0.2 mm | Balance between print speed and detail. |
| **Wall Line Count** | 3 or 4 walls | **Crucial:** Most strength comes from walls, not infill. |
| **Infill Density** | 30% - 40% | Provides internal support for screws. |
| **Infill Pattern** | Gyroid or Cubic | Isotropic strength for multi-directional stress. |
| **Supports** | Enabled (Tree/Organic) | Required for overhanging joint connectors. |
| **Build Plate Adhesion** | Brim or Skirt | Prevents warping on large base parts. |

![Slicer Settings Screenshot](path/to/your/slicer-image.png)
*(Replace the path above with your actual image URL)*

### 3. Printing Strategy
We recommend printing the parts in batches to organize assembly:

* **Batch A: The Base (Static)**
    * *Files:* `Base_Plate.stl`, `Turntable_Gear.stl`
    * *Orientation:* Print the large flat side down. No supports needed for the plate.
* **Batch B: The Arms (Load Bearing)**
    * *Files:* `Shoulder_Link.stl`, `Elbow_Link.stl`
    * *Orientation:* Print these laying flat on their side. **Do not print standing up vertically**, as the layer lines will create a weak point that snaps under load.
* **Batch C: The Gripper (Precision)**
    * *Files:* `Claw_Left.stl`, `Claw_Right.stl`, `Servo_Housing.stl`
    * *Settings:* Reduce print speed to 40mm/s for accurate gear teeth meshing.

### 4. Post-Processing
1.  **Support Removal:** Carefully remove support structures using needle-nose pliers. Pay attention to the servo horn slots; they must be clean for a tight fit.
2.  **Sanding:** Sand the rotating surfaces (where plastic touches plastic) with 200-grit sandpaper to reduce friction.
3.  **Hole expansion:** If the M3/M4 screw holes are too tight due to shrinkage, use a small drill bit or a hot screw to widen them slightly.
4.  **Fit Test:** Dry fit the servo motors into their slots. They should fit snugly without bending the plastic frame.

### 5. Troubleshooting Common Issues
* **Warping:** If the long arm pieces warp off the bed, increase the bed temperature by 5°C or use a glue stick.
* **Layer Separation:** If layers crack when tightening screws, increase your printing temperature by 5-10°C to improve layer bonding.
