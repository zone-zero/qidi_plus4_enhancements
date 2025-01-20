# Macros

This is a set of updated macros designed to do the following:

* Prevent bed meshing when the chamber or bed temperature are too high by supporting different meshing and printing temperatures
* Heat soak the bed for better meshes and prints
* Save and load different z offsets for different kinds of plates and temperatures
* Load different pre-computed meshes for different plates and temperatures. This is useful if you want to do several prints while the chamber is hot, and are not confident your printer can successfully build a KAMP mesh and detect z offset.
* Load a skew correction profile
* Provide more information in the console about what is happening during the print_start cycle.

Use at your own risk, and watch your printer carefully any time you use a new feature.

## Pre requisites
Level your bed as much as possible before using these macros. The assumption here is that there will be very little difference between a bed mesh taken at 60c followed by printing at 100c. If your bed is a twisted mess, this may not hold true.

## Background

The Qidi Plus4 printer (at least mine) suffers from issues creating a bed mesh and/or detecting the proper z offset between the induction sensor and the nozzle when the temperatures of the bed or chamber are too high. This can result in poor first layers, or in the worst cases, damage to your nozzle, print head and build plate. These macros attempt to work around the problem.

## Installation

comment out the following built in Qidi-built macros from gcode_macros.cfg:
* save_zoffset
* set_zoffset
* PRINT_START
* PRINT_END
* M4029

paste modified and new macros in to the end of your gcode_macros.cfg

## Options
Update your "Machine Start G-code" in your slicer as needed. Here are the available parameters with defaults. If you are ok with the default, just don't use the parameter.

### Temperatures
BED - bed temp for the print. no default\
HOTEND - hotend temp for the print. no default.\
CHAMBER - chamber temp for the print. defaults to 0\
MESH_MAX_CHAMBER - max allowed chamber temp when the mesh/auto z process is started. defaults to 30c\
MESH_MAX_BED - max allowed bedtemp for pulling the mesh. defaults to 60c.\

### Choices
ERROR_ON_COOL_FAIL - [0/1] halt the printer if the chamber is hotter than MESH_MAX_CHAMBER when we try to get the mesh. defaults to 1 (true)\
USE_PREBUILT_MESH - [0/1] use a prebuilt mesh if one is saved that matches MESH_NAME. defaults to 1 (true)\
USE_NAMED_ZOFFSET - [0/1] load a z offset specified by Z_OFFSET_NAME. defaults to 1 (true)\

### Strings
BED_TYPE - a string that represents the type of bed being used. Defaults to 'generic plate'. It is strongly recommended that you use Orca's multi-bed type support and set your machine start gcode to include the parameter: BED_TYPE="[curr_bed_type]"\
SKEW_PROFILE - name of a skew profile you want to load before printing. Defaults to none.\
MESH_NAME - name of a prebuit mesh you want to load instead or running KAMP. The value will be converted to lower case and spaces converted to underscores. If this exists, It will be loaded and the pre-mesh soak, chamber temp check, and nozzle cleaning will be skipped. Defaults to the bed type and the bed temperature. Example: If your BED_TYPE is "Textured PEI Plate" and your print temp is 100, the default mesh name would be "textured_pei_plate-100c"\
Z_OFFSET_NAME - name of a z-offset you want to use. The value will be converted to lower case and spaces converted to underscores. Defaults to "z-offset-"{MESH_NAME}. Example: "z-offset-textured_pei_plate-100c". This allows different z offset for different temps, if desired.\

### Times
PRE_MESH_SOAK_TIME: How long (in minutes) you want the bed to soak before pulling a mesh. The mesh soak is only run if the print temp is higher than MESH_MAX_BED. Defaults to 5 minutes.\
PRE_PRINT_SOAK_TIME: How long (in minutes) you want the bed to soak before printing. Defauls to 5 minutes.\
