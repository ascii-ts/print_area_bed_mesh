# Print Area Bed Mesh

## Description

- This macro will create a bed_mesh based on the size of your print.

## Credit

Complete credit goes to ChipCE (https://gist.github.com/ChipCE/95fdbd3c2f3a064397f9610f915f7d02) for the idea and sample code. I completely rewrote the macro to be more dynamic and for some added features.

## Features:
- **Reusable Bed Mesh** - bed mesh will be reused if print area is smaller or equal to the previous one with ability to override
- **Easy To Add** - Nothing to update in the script since the existing bltouch or probe offsets, existing bed_mesh min/max, probe_count values are used from printer.cfg
- **Seamless Bed Mesh Calibration** - BED_MESH_CALIBRATE replaced with new script
- **Separate axis probe counts** - if print area X or y is 50% than available area, probe_count for that axis is changed from the default to 3
- **Supports Bed Mesh Relative Reference Index** - if "relative_reference_index" is set in bed_mesh, it will be reconfigured to use the middle point on the bed
- **Klicky Probe Compatible** - Works with Klicky Probe on Voron

## Disclaimer

This is currently in development. While it works on my 2 printers, it's impossible to account for everyone's various config. When first implementing this, be prepared to click the "Emergency Stop" button if it tries to probe off the bed.

## Change Log

- Combined klicky probe version with regular script
- Added 20mm buffer to bed mesh

## Setup

### Add new macro to Klipper
- Through Fluidd or Mainsail, upload the "print_area_bed_mesh.cfg" file to the folder where your printer.cfg file is located and add the following line at the top of your printer.cfg: <pre>[include print_area_bed_mesh.cfg]</pre>

### Modify "start_print" macro
- Modify your "print_start" macro in your printer.cfg to include the 2 parameters (PRINT_MIN and PRINT_MAX) <pre>
[gcode_macro print_start]
variable_parameter_PRINT_MIN : 0,0
variable_parameter_PRINT_MAX : 0,0
gcode:
</pre>

- Where you normally perform a BED_MESH_CALIBRATE in your start_print macro, replace it (or add it if you didn't previously have it) with the following line:<pre>
BED_MESH_CALIBRATE PRINT_MIN={params.PRINT_MIN} PRINT_MAX={params.PRINT_MAX}
</pre>

### Update your Slicer
- Modify your printer start g-code in your slicer to include the PRINT_MIN and PRINT_MAX parameters:

Examples:


PrusaSlicer/SuperSlicer:
<pre>print_start EXTRUDER={first_layer_temperature[initial_extruder] + extruder_temperature_offset[initial_extruder]} BED=[first_layer_bed_temperature] CHAMBER=[chamber_temperature] PRINT_MIN={first_layer_print_min[0]},{first_layer_print_min[1]} PRINT_MAX={first_layer_print_max[0]},{first_layer_print_max[0]}</pre>

Cura (add this to your start gcofe at the end of the start_print command:)
<pre>PRINT_MIN=%MINX%,%MINY% PRINT_MAX=%MAXX%,%MAXY%</pre>

*(Cura slicer plugin) To make the macro to work in Cura slicer, you need to install the post process plugin by frankbags - In cura menu Help -> Show configuration folder. - Copy the python script from the above link in to plugins folder. - Restart Cura - In cura menu Extensions -> Post processing and select Mesh Print Size
