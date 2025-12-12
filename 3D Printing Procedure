# 🛠️ 3D Printing Procedure

This document describes the recommended workflow to 3D print parts for the 4‑DOF robotic arm using FDM printers. All STL files referenced here are stored in the `/STL_Files` directory of this repository. Use this as a reproducible guide for printing, finishing, and preparing parts for assembly.

---

## 1 — Prerequisites

- 3D printer with a minimum build volume of 200 × 200 × 200 mm (examples: Creality Ender 3, Prusa i3).
- Filament: PLA+ or PETG recommended.
  - Note: Standard PLA can be brittle for high‑torque joints; prefer PLA+ or PETG for durability.
- Slicer software: Cura, PrusaSlicer, or Simplify3D.
- Tools: flush cutters, needle‑nose pliers, small file set, 200–400 grit sandpaper, electric drill or reamer for hole tuning, hex drivers for screws.
- Power: separate 5V supply for servos (do not power multiple servos from the ESP32 5V regulator).
- Directory: place all STL exports under `/STL_Files` in the repo.

---

## 2 — Slicer Baseline Settings

These baseline settings balance strength and print time. Tune per filament and printer.

- Layer height: 0.20 mm (use 0.12–0.16 mm for fine detail)
- Wall / Perimeter count: 3–4
- Infill density: 30%–40%
- Infill pattern: Gyroid or Cubic (for isotropic strength)
- Supports: Enabled (Tree/Organic preferred for overhangs)
- Build plate adhesion: Brim for large parts, Skirt for small parts
- Print speed: 40–60 mm/s for load‑bearing parts, 30–45 mm/s for gears/precision parts
- Nozzle temp / Bed temp: follow filament manufacturer; increase nozzle temp by +5–10°C if you see poor layer bonding
- Retract, cooling, and bridging: tune for your printer

Replace the example image below with your own:
![Slicer Settings Example](path/to/your/slicer-image.png)

---

## 3 — Part Batching & Orientation

Print in logical groups to simplify QA and assembly. Orient parts to optimize strength (avoid layer lines perpendicular to bending loads).

- Batch A — Base & rotating parts
  - Files: `Base_Plate.stl`, `Turntable_Gear.stl`
  - Orientation: print flat on the largest face (large flat side down)
  - Notes: use brim; usually no supports for the plate

- Batch B — Load‑bearing links (shoulder, elbow)
  - Files: `Shoulder_Link.stl`, `Elbow_Link.stl`
  - Orientation: print on their side (do NOT print vertically/standing)
  - Notes: side orientation reduces bending weakness along layer lines

- Batch C — Gripper and small precision parts
  - Files: `Claw_Left.stl`, `Claw_Right.stl`, `Servo_Housing.stl`
  - Orientation: print to maximize detail on contact surfaces
  - Notes: lower print speed (~40 mm/s) for accurate gear teeth and mating features

---

## 4 — Per‑Part Slicing Recommendations

- Thin clips/tabs: increase wall count, reduce layer height to 0.12–0.16 mm.
- Gear teeth: test-fit and tune horizontal expansion or XY compensation to avoid binding.
- Servo mounts: leave 0.2–0.5 mm clearance depending on printer calibration; consider heat‑set inserts for repeated disassembly.
- Long, thin parts: enable extra walls and consider 50%+ infill for critical brackets.

---

## 5 — Post‑Processing & Fit

1. Remove supports carefully with flush cutters and pliers.
2. Deburr and sand mating surfaces with 200–400 grit sandpaper to reduce friction.
3. Ream bolt holes where necessary with the correct drill bit (M3 → 3.0 mm, M4 → 4.0 mm), or use a heated screw to clear the hole.
4. Dry‑fit all servos and fasteners. Confirm snug fit without forcing parts.
5. Consider heat‑set threaded inserts for frequently removed fasteners.
6. Lightly lubricate sliding surfaces (PTFE or silicone) if needed — avoid contamination near electronics.

---

## 6 — Fasteners & Assembly Notes

- Use stainless steel M3 or M4 screws as per part design. Countersunk or pan head as required.
- Use thread locker (Loctite Blue 242) on vibration‑prone fasteners, or use lock washers.
- Tighten screws incrementally and check for binding. Avoid over‑torquing plastic threads.
- For critical joints, use metal reinforcement plates or through bolts with nuts.

---

## 7 — Calibration & Servo Mapping

- Verify each servo's neutral (90°) maps to the arm’s intended neutral pose. Apply firmware offsets if needed.
- If a servo appears mirrored, either invert its angle mapping in firmware or flip the servo horn/mechanical mounting.
- Document per‑servo offsets and store them in config or a calibration file (SPIFFS/LittleFS) for repeatable setups.

Example firmware mapping idea:
- Add an `offsets[]` array:
  ```cpp
  int offsets[4] = {0, -10, 5, 0};
  servos[i].write(constrain(currentAngles[i] + offsets[i], ANGLE_MIN, ANGLE_MAX));
  ```

---

## 8 — Troubleshooting (Common Print Problems)

- Warping: increase bed temp by 5°C, use brim, use PEI/garolite sheet or glue stick.
- Layer separation: increase nozzle temp by 5–10°C, reduce print cooling for early layers.
- Dimensional inaccuracy: calibrate extrusion multiplier and XY steps; print calibration cubes and single‑wall tests.
- Stripped plastic threads: use heat‑set inserts or through bolts with nuts.

---

## 9 — Safety & Best Practices

- Keep hands and loose objects away from moving parts while testing.
- Always use a power supply with adequate peak current for servos.
- Ensure common ground between the ESP32 and servo power supply.
- Test movement first with slow, small steps and monitor for collisions.

---

## 10 — Durability Tips

- Use PETG for parts needing impact resistance or slight flexibility.
- Reinforce high‑load contacts with metal plates where possible.
- For frequently serviced joints, prefer heat‑set inserts for long‑term reliability.

---

## 11 — Optional: Additions I can prepare for the repo

- Per‑part slicer profiles (Cura / PrusaSlicer) exported and included in `/slicer_profiles`.
- Annotated photos showing recommended orientations and support locations.
- A step‑by‑step assembly guide (with photos) in PDF or Markdown.

---

If you want, I can add this file directly to your repository. Tell me:
- branch name to push to (suggested: `add/esp32-firmware`),  
- commit message to use, and  
- whether to overwrite an existing file if present (yes/no).

If you prefer to add it yourself, copy the above content into `3D_PRINTING_PROCEDURE.md` at the repo root or `/docs`.  
