*KiCad Nightly Reference Manual*

**Copyright**

This document is Copyright © 2010-2021 by its contributors as listed
below. You may distribute it and/or modify it under the terms of either
the GNU General Public License
(<https://www.gnu.org/licenses/gpl.html>), version 3 or later, or the
Creative Commons Attribution License
(<https://creativecommons.org/licenses/by/3.0/>), version 3.0 or later.

All trademarks within this guide belong to their legitimate owners.

**Contributors**

The KiCad Team.

**Feedback**

The KiCad project welcomes feedback, bug reports, and suggestions
related to the software or its documentation. For more information on
how to submit feedback or report an issue, please see the instructions
at <https://www.kicad.org/help/report-an-issue/>

**Software and Documentation Version**

This user manual is based on KiCad 9.99. Functionality and appearance
may be different in other versions of KiCad.

Documentation revision: `dummy-commit`.

# Introduction to GerbView

GerbView is a Gerber file (RS-274X format) and Excellon drill file
viewer. Up to 32 files can be displayed at once.

For more information about the Gerber file format please read [the
Gerber File Format
Specification](http://www.ucamco.com/files/downloads/file/81/the_gerber_file_format_specification.pdf).
Details about drill file format can be found at [the Excellon format
description](http://web.archive.org/web/20071030075236/http://www.excellon.com/manuals/program.htm).

# Interface

## Main window

<figure>
<img src="images/gerbview_main_screen.png" style="width:95.0%"
alt="gerbview_main_screen_png" />
</figure>

## Top toolbar

|                                                                                           |                                                             |
|-------------------------------------------------------------------------------------------|-------------------------------------------------------------|
| ![delete_gerber_png](images/icons/delete_gerber_24.png)                                   | Clear all layers                                            |
| ![load_gerber_png](images/icons/load_gerber_24.png)                                       | Load Gerber files                                           |
| ![gerbview_drill_file_png](images/icons/load_drill_24.png)                                | Load Excellon drill files                                   |
| ![sheetset_png](images/icons/sheetset_24.png)                                             | Set page size                                               |
| ![print_button_png](images/icons/print_button_24.png)                                     | Print                                                       |
| ![zoom_redraw_png](images/icons/refresh_24.png)                                           | Redraw view                                                 |
| ![zoom_in_png](images/icons/zoom_in_24.png) ![zoom_out_png](images/icons/zoom_out_24.png) | Zoom in or out                                              |
| ![zoom_fit_in_page_png](images/icons/zoom_fit_in_page_24.png)                             | Zoom to fit page                                            |
| ![zoom_area_png](images/icons/zoom_area_24.png)                                           | Zoom to selection                                           |
| <img src="images/gerbview_top_layer.png" style="width:70.0%"                              
 alt="gerbview_top_layer_png" />                                                            | Select active layer                                         |
| <img src="images/gerbview_top_info.png" style="width:70.0%"                               
 alt="gerbview_top_info_png" />                                                             | Display info about active layer                             |
| <img src="images/gerbview_x2_component.png" style="width:70.0%"                           
 alt="gerbview_x2_component_png" />                                                         | Highlight items belonging to selected component (Gerber X2) |
| <img src="images/gerbview_x2_net.png" style="width:70.0%"                                 
 alt="gerbview_x2_net_png" />                                                               | Highlight items belonging to selected net (Gerber X2)       |
| <img src="images/gerbview_x2_attribute.png" style="width:70.0%"                           
 alt="gerbview_x2_attributeo_png" />                                                        | Highlight items with the selected attribute (Gerber X2)     |
| <img src="images/gerbview_top_dcode.png" style="width:60.0%"                              
 alt="gerbview_top_dcode_png" />                                                            | Highlight items of selected D Code on the active layer      |

## Left toolbar

|                                                                                                                                          |                                                          |
|------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------|
| ![cursor_png](images/icons/cursor_24.png)                                                                                                | Select items                                             |
| ![measurement_png](images/icons/measurement_24.png)                                                                                      | Measure between two points                               |
| ![grid_png](images/icons/grid_24.png)                                                                                                    | Toggle grid visibility                                   |
| ![polar_coord_png](images/icons/polar_coord_24.png)                                                                                      | Toggle polar coordinates display                         |
| ![unit_inch_png](images/icons/unit_inch_24.png) ![unit_mm_png](images/icons/unit_mil_24.png) ![unit_mm_png](images/icons/unit_mm_24.png) | Select inch, mils, or millimeter units                   |
| ![cursor_shape_png](images/icons/cursor_shape_24.png)                                                                                    | Toggle full-screen cursor                                |
| ![pad_sketch_png](images/icons/pad_sketch_24.png)                                                                                        | Display flashed items in sketch (outline) mode           |
| ![track_sketch_png](images/icons/showtrack_24.png)                                                                                       | Display lines in sketch (outline) mode                   |
| ![opt_show_polygon_png](images/icons/opt_show_polygon_24.png)                                                                            | Display polygons in sketch (outline) mode                |
| ![gerbview_show_negative_objects_png](images/icons/gerbview_show_negative_objects_24.png)                                                | Show negative objects in ghost color                     |
| ![show_dcodenumber_png](images/icons/show_dcodenumber_24.png)                                                                            | Show/hide D Codes                                        |
| ![gbr_select_mode2_png](images/icons/gbr_select_mode2_24.png)                                                                            | Display layers in diff (compare) mode                    |
| ![contrast_mode_png](images/icons/contrast_mode_24.png)                                                                                  | Toggle inactive layers between normal and dimmed display |
| ![layers_manager_png](images/icons/layers_manager_24.png)                                                                                | Show/hide layer manager                                  |
| ![flip_board_24](images/icons/flip_board_24.png)                                                                                         | Show Gerbers as mirror image                             |

## Layers Manager

<figure>
<img src="images/gerbview_layer_manager.png" style="width:40.0%"
alt="gerbview_layer_manager_png" />
</figure>

The Layers Manager controls and displays visibility of all layers. An
arrow indicates the active layer, and each layer can be shown or hidden
with the checkboxes.

Mouse button assignments:

- Left click: select the active layer

- Right click: show/hide/sort layers options

- Middle click or double click (on color swatch): select the layer color

The Layers tab allows you to control the visibility and color of all
loaded Gerber and drill layers. The Items tab allows you to control the
color and display of the grid, D Codes, and negative objects.

# Commands in menu bar

## File menu

<figure>
<img src="images/gerbview_file_menu.png" style="width:45.0%"
alt="gerbview_file_menu_png" />
</figure>

- **Export to PCB Editor** is a limited capability to export Gerber
  files into a KiCad PCB. The final result depends on what features of
  the RS-274X format are used in the original Gerber files: rasterized
  items cannot be converted (typically negative objects), flashed items
  are converted to vias, lines are converted to track segments (or
  graphic lines for non-copper layers).

## Tools menu

<figure>
<img src="images/gerbview_tools_menu.png" style="width:25.0%"
alt="gerbview_tools_menu_png" />
</figure>

- **List DCodes** shows the D Code information for all layers.

- **Show Source** displays the Gerber file contents of the active layer
  in a text editor.

- **Measure Tool** allows measuring the distance between two points.

- **Clear Current Layer** erases the contents of the active layer.

# Printing

To print layers, use the
![print_button_png](images/icons/print_button_24.png) icon or the **File
→ Print** menu.

<div class="caution">

Be sure items are inside the printable area. Use
![sheetset_png](images/icons/sheetset_24.png) to select a suitable page
format.

Note that many photoplotters support a large plottable area, much bigger
than the page sizes used by most printers. Moving the entire layer set
may be required.

</div>
