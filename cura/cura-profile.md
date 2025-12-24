# Cura Profile – Prusa i3 TDP-SPT02 (RAMPS 1.4, Marlin bugfix-2.1.x)

Ready-to-enter Cura settings that match the current Marlin config in this repo. Built for Mega2560 + RAMPS 1.4 (EFB), 0.4 mm brass nozzle, 1.75 mm filament, heated bed, RepRapDiscount Smart Controller. Use as a starting point and tune thermals and retraction on your hardware.

## Machine Settings (Cura > Preferences > Printers > Add Custom FDM)
- Printer name: Prusa i3 TDP-SPT02
- G-code flavor: Marlin
- X / Y / Z: 200 / 200 / 200 mm
- Origin at center: off (front-left is 0,0)
- Heated bed: on
- Nozzle count: 1
- Nozzle diameter: 0.4 mm
- Compatible material diameter: 1.75 mm
- Build plate shape: Rectangular
- Firmware retraction: off (Marlin default)
- Relative extrusion: off (Cura default; Marlin uses absolute E by default)

## Start G-code (copy into Machine Settings)
```gcode
; Start G-code for Marlin RAMPS i3 200x200
M140 S{material_bed_temperature_layer_0} ; set bed temp
M104 S{material_print_temperature_layer_0} ; set nozzle temp
M190 S{material_bed_temperature_layer_0} ; wait for bed
M109 S{material_print_temperature_layer_0} ; wait for nozzle
G28 ; home all axes
G92 E0 ; zero the extruder
G1 Z0.2 F3000 ; move to first layer height
G1 X5 Y5 F6000 ; move to corner
G1 X150 E15 F800 ; purge line
G92 E0 ; reset extruder
```

## End G-code
```gcode
; End G-code for Marlin RAMPS i3
G91 ; relative moves
G1 E-2 F1800 ; retract a bit
G1 Z10 F1200 ; lift
G90 ; absolute moves
G1 X0 Y200 F6000 ; park at front-left
M104 S0 ; hotend off
M140 S0 ; bed off
M106 S0 ; fans off
M84 ; disable motors
```

## Speeds (aligned to firmware limits)
- Print speed: 45–60 mm/s (stay well below firmware 300 mm/s max)
- Travel speed: 120 mm/s (below XY max feedrate 300 mm/s)
- Initial layer speed: 20 mm/s (inner/outer)
- Wall speed: 40–50 mm/s
- Top/bottom speed: 35–45 mm/s
- Enable Acceleration control: optional; if you enable it, set:
  - Print/Wall/Travel Acceleration: 3000 mm/s² (matches Marlin DEFAULT_ACCELERATION/DEFAULT_TRAVEL_ACCELERATION)
  - Jerk control: leave disabled so Marlin’s Junction Deviation stays in charge

## Retraction (Bowden-style guess; tune on your machine)
- Retraction distance: 4.5–6.0 mm
- Retraction speed: 35–45 mm/s
- Minimum travel: 1.5 mm
- Combing: Within Infill (or Not In Skin if you see scars)
- Z Hop: off or 0.2–0.4 mm if needed for scar-free travel

## Cooling
- Fan: 0% for first 1–2 layers, then 100% for PLA. For PETG cap at 40–60%.

## Temperatures (starting points)
- PLA: Nozzle 200 °C (first layer), 195–205 °C after; Bed 60 °C (first), 55–60 °C after.
- PETG: Nozzle 235 °C (first), 230–240 °C after; Bed 80 °C (first), 75–85 °C after.

## Adhesion & First Layer
- Initial layer height: 0.2 mm
- Initial layer line width: 110–120%
- Initial layer flow: 100–105%
- Build plate adhesion: Skirt (3–4 lines). Use Brim for small parts/ABS.

## Walls & Infill
- Wall line count: 2 (or 3 for stronger parts)
- Top/bottom layers: 4–5 at 0.2 mm (0.8–1.0 mm thick)
- Infill: 15–25% Grid/Cubic for general prints

## Support
- Enable as needed; Tree supports work well for overhangs. Interface on; Air gap 0.2–0.3 mm.

## Material Flow & E-steps
- Flow: 100% as a start. Calibrate with single-wall test.
- E-steps in firmware: 500 steps/mm (from Marlin). After calibrating extrusion, update via `M92 E<new>` then `M500` (EEPROM enabled).

## Safety & Checks
- Verify endstop wiring: config assumes NC switches (logic LOW when triggered). If you use NO switches, invert in firmware or rewire.
- PID tune on your machine (`M303 E0 S200 C8`, `M303 E-1 S60 C8` for bed) and store with `M500`.
- After flashing: run `M502` then `M500` once to initialize EEPROM.

## Notes
- Bed size and origin match Marlin: 200 x 200 x 200 mm, (0,0) front-left.
- Firmware uses Junction Deviation (0.013) instead of classic jerk; keep Cura jerk control off.
- Max feedrates (firmware): X/Y 300 mm/s, Z 5 mm/s, E 25 mm/s. Keep Cura speeds comfortably below these.
- Max accelerations (firmware): X/Y 3000, Z 100, E 10000 mm/s². Use the 3000/100 defaults above if you enable accel control.
- Steps/mm (firmware): X/Y 80, Z 400, E 500. Match to your calibrated values if you change hardware.

## Importable Cura Profile (.curaprofile snippet)
Save the block below as `Prusa_i3_TDP-SPT02.curaprofile` and import it in Cura (Preferences → Profiles → Import). Values are conservative starters that match the firmware.

```json
{
  "version": 4,
  "name": "Prusa i3 TDP-SPT02",
  "inherits": "fdmprinter",
  "metadata": {
    "machine": "Custom",
    "setting_version": 22
  },
  "overrides": {
    "machine_name": "Prusa i3 TDP-SPT02",
    "machine_width": {"default_value": 200},
    "machine_depth": {"default_value": 200},
    "machine_height": {"default_value": 200},
    "machine_shape": {"default_value": "rectangular"},
    "machine_heated_bed": {"default_value": true},
    "material_diameter": {"default_value": 1.75},
    "machine_nozzle_size": {"default_value": 0.4},
    "machine_gcode_flavor": {"default_value": "Marlin"},
    "machine_start_gcode": {
      "default_value": "M140 S{material_bed_temperature_layer_0}\nM104 S{material_print_temperature_layer_0}\nM190 S{material_bed_temperature_layer_0}\nM109 S{material_print_temperature_layer_0}\nG28\nG92 E0\nG1 Z0.2 F3000\nG1 X5 Y5 F6000\nG1 X150 E15 F800\nG92 E0\n"
    },
    "machine_end_gcode": {
      "default_value": "G91\nG1 E-2 F1800\nG1 Z10 F1200\nG90\nG1 X0 Y200 F6000\nM104 S0\nM140 S0\nM106 S0\nM84\n"
    },
    "travel_speed": {"default_value": 120},
    "speed_print": {"default_value": 50},
    "speed_wall": {"default_value": 45},
    "speed_topbottom": {"default_value": 40},
    "speed_travel_layer_0": {"default_value": 40},
    "speed_layer_0": {"default_value": 20},
    "acceleration_enabled": {"default_value": false},
    "jerk_enabled": {"default_value": false},
    "retraction_enable": {"default_value": true},
    "retraction_amount": {"default_value": 5.5},
    "retraction_speed": {"default_value": 40},
    "retraction_min_travel": {"default_value": 1.5},
    "retraction_hop_enabled": {"default_value": false},
    "cool_fan_enabled": {"default_value": true},
    "cool_fan_speed": {"default_value": 100},
    "cool_fan_speed_0": {"default_value": 0},
    "adhesion_type": {"default_value": "skirt"},
    "skirt_line_count": {"default_value": 3},
    "wall_line_count": {"default_value": 2},
    "top_layers": {"default_value": 5},
    "bottom_layers": {"default_value": 5},
    "infill_sparse_density": {"default_value": 20},
    "material_print_temperature": {"default_value": 200},
    "material_print_temperature_layer_0": {"default_value": 200},
    "material_bed_temperature": {"default_value": 60},
    "material_bed_temperature_layer_0": {"default_value": 60},
    "layer_height": {"default_value": 0.2},
    "initial_layer_height": {"default_value": 0.2},
    "line_width": {"default_value": 0.4},
    "infill_pattern": {"default_value": "grid"}
  }
}
```
