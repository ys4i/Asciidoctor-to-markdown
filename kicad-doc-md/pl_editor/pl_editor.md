*KiCad Nightly Reference Manual*

**Copyright**

This document is Copyright © 2024 by its contributors as listed below.
You may distribute it and/or modify it under the terms of either the GNU
General Public License (<http://www.gnu.org/licenses/gpl.html>), version
3 or later, or the Creative Commons Attribution License
(<http://creativecommons.org/licenses/by/3.0/>), version 3.0 or later.

**Contributors**

Jean-Pierre Charras, Graham Keeth

**Feedback**

The KiCad project welcomes feedback, bug reports, and suggestions
related to the software or its documentation. For more information on
how to submit feedback or report an issue, please see the instructions
at <https://www.kicad.org/help/report-an-issue/>

**Software and Documentation Version**

This user manual is based on KiCad 9.99. Functionality and appearance
may be different in other versions of KiCad.

Documentation revision: `dummy-commit`.

# Introduction to the KiCad Drawing Sheet Editor

The Drawing Sheet Editor is a tool to create custom drawing sheets for
use in the KiCad Schematic and Board Editors. Drawing sheets can include
custom title blocks, frames, logos, as well as other text and graphics.

The frame, title block, and other graphic items (logos) are collectively
called a **drawing sheet**.

Basic drawing sheet items are:

- **Lines**

- **Rectangles**

- **Text** (with keywords that will be replaced by the actual text, like
  the date, page number…​) in the Schematic or Board Editors.

- **Poly-polygons** (mainly to place logos and special graphic shapes)

- **Bitmaps**.

<div class="warning">

Bitmaps can be plotted only by few plotters (PDF and PS only) Therefore,
for other plotters, only a bounding box will be plotted.

</div>

- Items can be repeated, and text and poly_polygons can be rotated.

# Drawing Sheet Editor files

The Drawing Sheet Editor reads and writes KiCad drawing sheet files
(`.kicad_wks`). These files can be used as custom drawing sheets for
schematic and PCB designs by selecting a custom drawing sheet in the
**Page Setup** dialog in each editor.

When the Drawing Sheet Editor is first opened, it displays the default
KiCad drawing sheet is used until a different drawing sheet file is
opened.

# Theory of operations

## Basic drawing sheet item properties

Basic drawing sheet items are:

- **Lines**

- **Rectangles**

- **Text** (with keywords, with will be replaced by the actual text,
  like the date, page number…​) in the Schematic or PCB Editors.

- **Poly-polygons** (mainly to place logos and special graphic shapes).
  These poly polygons are created by the **Image Converter** tool, and
  cannot be built inside the Drawing Sheet Editor, because it is not
  possible to create such shapes by hand.

- **Bitmaps** to place logos.

<div class="warning">

Bitmaps can be plotted only by few plotters: PDF and PS only.

</div>

Therefore:

- **Text, poly-polygons** and **bitmaps** are defined by a position, and
  can be rotated.

- **Lines** (in fact segments) and **rectangles** are defined by two
  points: a start point and a end point. They cannot be rotated (this is
  useless for segments).

These basic items can be repeated.

Repeated text also accepts an increment value for labels (has meaning
only if the text is one letter or one digit).

## Coordinates definition

Each position, start point and end point of items is always relative to
a page corner. This feature allows you to define a drawing sheet which
is not dependent on the paper size.

## Reference corners and coordinates:

![page property 1](images/en/page_property_1.png)

- When the page size is changed, the position of the item, relative to
  its reference corner does not change.

- Usually, title blocks are attached to the right bottom corner, and
  therefore this corner is the default corner, when creating an item.

For rectangles and segments, which have two defined points, each point
has its reference corner.

## Rotation

Items which have a position defined by just one point (text and
poly-polygons) can be rotated:

Normal: Rotation = 0

<figure>
<img src="images/en/text_noriented.png" style="width:50.0%"
alt="text noriented" />
</figure>

Rotated: Rotation = 20 and 10 degrees.

<figure>
<img src="images/en/text_rotated.png" style="width:50.0%"
alt="text rotated" />
</figure>

## Repeat option

Items can be repeated:

This is useful to create grid and grid labels.

<figure>
<img src="images/en/page_property_2.png" style="width:95.0%"
alt="page property 2" />
</figure>

# Text and keywords

## Keywords

Text can be simple strings or can include keywords.

Keywords are replaced by actual values when the drawing sheet is used in
a schematic or PCB design. They behave like [text
variables](../eeschema/eeschema.xml#text-variables) in the Schematic and
Board Editors, except the values are either automatically set by the
editor or set by the user in the Page Setup dialog of the respective
editor.

The keyword syntax is `${KEYWORD}`. The keyword, including the
surrounding `${}`, will be replaced by the keyword’s value.

| Keyword name            | Description                                                                                                                |
|-------------------------|----------------------------------------------------------------------------------------------------------------------------|
| `KICAD_VERSION`         | Version number of KiCad.                                                                                                   |
| `#`                     | Sheet number.                                                                                                              |
| `##`                    | Total number of sheets.                                                                                                    |
| `COMMENT1` - `COMMENT9` | Contents of the `Comment<n>` field in Page Setup.                                                                          |
| `COMPANY`               | Contents of the `Company` field in Page Setup.                                                                             |
| `FILENAME`              | Filename of the schematic or PCB design file, with a file extension.                                                       |
| `ISSUE_DATE`            | Contents of the `Issue Date` field in Page Setup.                                                                          |
| `LAYER`                 | Name of the current PCB layer. This is blank in the Schematic and Board Editors. It is only shown in plots of PCB designs. |
| `PAPER`                 | Current sheet’s paper size, which is set in Page Setup.                                                                    |
| `REVISION`              | Contents of the `Revision` field in Page Setup.                                                                            |
| `SHEETNAME`             | Sheet name of the current sheet. This is blank in the Board Editor.                                                        |
| `SHEETPATH`             | Sheet path of the current sheet. This is blank in the Board Editor.                                                        |
| `TITLE`                 | Contents of the `Title` field in Page Setup.                                                                               |

For example, `Size: ${PAPER}` displays "Size: A4" when the paper size is
set to A4.

When the Preview display mode is active (![pagelayout normal view mode
24](images/icons/pagelayout_normal_view_mode_24.png)), the title block
is displayed like in the Schematic and PCB Editors, with keywords
replaced with the corresponding values. You can configure the displayed
values in the Page Preview Settings dialog (![sheetset
24](images/icons/sheetset_24.png)).

<figure>
<img src="images/show_fields_edit.png" style="width:70.0%"
alt="show fields edit" />
</figure>

When the Edit mode is active (![pagelayout special view mode
24](images/icons/pagelayout_special_view_mode_24.png)), the title block
is displayed without replacing keywords.

<figure>
<img src="images/show_fields_preview.png" style="width:70.0%"
alt="show fields preview" />
</figure>

## Multi-line text

Text can be multi-line.

There are 2 ways to insert a new line in text:

1.  Insert the `\n` 2 chars sequence (mainly in Page setup dialog in
    KiCad).

2.  Insert a new line in the Drawing Sheet Editor Design window.

Here is an example:

Setup

<figure>
<img src="images/options_multi_line.png" style="width:50.0%"
alt="options multi line" />
</figure>

Output

<figure>
<img src="images/multi_line.png" style="width:65.0%" alt="multi line" />
</figure>

## Multi-line text in Page Setup dialog

In the Page Setup dialog, text controls do not accept multi-line text.

The `\n` 2 character sequence should be inserted to force a new line
inside a text object.

Here is a two line text object, in the Comment2 field:

![insert newline code](images/insert_newline_code.png)

Here is the actual text:

![multi line 2](images/multi_line_2.png)

However, if you really want the `\n` inside the text, enter `\\n`.

![insert slashnewline code](images/insert_slashnewline_code.png)

And the displayed text:

![multi line 3](images/multi_line_3.png)

# Constraints

## Page 1 constraint

When using the Schematic Editor, the full schematic often uses more than
one page.

Usually drawing sheet items are shown on all pages, but you can also set
each item to be shown only on the first page or on all pages except the
first page. To change which pages an item is shown on, use the dropdown
in the the item’s **Item Properties** panel. Options are **show on all
pages**, **first page only**, and **subsequent pages only**.

![page options](images/page_options.png)

## Text maximum size constraint

Text items have a maximum size constraint. You can set a **maximum
height** and **maximum width**, which together form a bounding box
defining the maximum size of the text object.

![text max size options](images/text_max_size_options.png)

If the text object is bigger than the maximum size in a given dimension,
the text object will be dynamically compressed in that dimension in
order to fit within the bounding box. This will result in the text being
visually distorted. If the text fits within the bounding box, the text
will not be compressed.

When either parameter is set to `0`, KiCad will not enforce a maximum
size in that dimension.

Some text with the maximum height and width set to `0`:

![text no max width](images/text_no_max_width.png)

The same text compressed because the maximum width is set smaller than
the width of the text:

![text max width](images/text_max_width.png)

# Invoking the Drawing Sheet Editor

The Drawing Sheet Editor is typically invoked from a command line, or
from the KiCad Project Manager.

From a command line, the syntax is pl_editor \<\*.kicad_wks file to
open\>.

# Drawing Sheet Editor Commands

## Main Screen

The image below shows the main window of the Drawing Sheet Editor.

<figure>
<img src="images/main_window.png" style="width:95.0%"
alt="main window" />
</figure>

The main part of the screen is the editing canvas for the open drawing
sheet.

The right pane is a properties editor for editing the selected item. It
only appears when an item is selected in the canvas.

## Main Window Toolbar

The top toolbar allows for easy access to the following commands:

<table>
<colgroup>
<col style="width: 28%" />
<col style="width: 72%" />
</colgroup>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p><img
src="images/icons/new_generic_24.png" alt="new generic 24" /></p></td>
<td style="text-align: left;"><p>Create a new drawing sheet.</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><img
src="images/icons/directory_open_24.png"
alt="directory open 24" /></p></td>
<td style="text-align: left;"><p>Load a drawing sheet file.</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><img src="images/icons/save_24.png"
alt="save 24" /></p></td>
<td style="text-align: left;"><p>Save the current drawing sheet in a
<code>.kicad_wks</code> file.</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><img src="images/icons/sheetset_24.png"
alt="sheetset 24" /></p></td>
<td style="text-align: left;"><p>Display the page size selector and the
title block user data editor.</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><img
src="images/icons/print_button_24.png" alt="print button 24" /></p></td>
<td style="text-align: left;"><p>Prints the current page.</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><img src="images/icons/undo_24.png"
alt="undo 24" /> <img src="images/icons/redo_24.png"
alt="redo 24" /></p></td>
<td style="text-align: left;"><p>Undo/redo tools.</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><img src="images/icons/zoom_in_24.png"
alt="zoom in 24" /> <img src="images/icons/zoom_out_24.png"
alt="zoom out 24" /> <img src="images/icons/refresh_24.png"
alt="refresh 24" /> <img src="images/icons/zoom_fit_in_page_24.png"
alt="zoom fit in page 24" /></p></td>
<td style="text-align: left;"><p>Zoom in, out, redraw and auto,
respectively.</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><img
src="images/icons/pagelayout_normal_view_mode_24.png"
alt="pagelayout normal view mode 24" /></p></td>
<td style="text-align: left;"><p>Show the drawing sheet in Preview mode:
text is shown like in the Schematic or PCB Editors, with text keywords
replaced by user text.</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><img
src="images/icons/pagelayout_special_view_mode_24.png"
alt="pagelayout special view mode 24" /></p></td>
<td style="text-align: left;"><p>Show the drawing sheet in Edit mode:
text is displayed "as is", without any keyword replacement.</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><img
src="images/en/set_base_corner.png" alt="set base corner" /></p></td>
<td style="text-align: left;"><p>Reference corner selection, for
coordinates displayed to the status bar.</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><img
src="images/en/set_current_page.png" alt="set current page" /></p></td>
<td style="text-align: left;"><p>Selection of the page number (page
&amp; or other pages).</p>
<p>This selection has meaning only if some items than have a page
option, are not shown on all pages (in a schematic for instance, which
contains more than one page).</p></td>
</tr>
</tbody>
</table>

## Commands in drawing area (draw panel)

### Keyboard Commands

|             |                                                         |
|-------------|---------------------------------------------------------|
| F1          | Zoom In                                                 |
| F2          | Zoom Out                                                |
| F3          | Refresh Display                                         |
| F4          | Move cursor to center of display window                 |
| Home        | Fit footprint into display window                       |
| Space Bar   | Set relative coordinates to the current cursor position |
| Right Arrow | Move cursor right one grid position                     |
| Left Arrow  | Move cursor left one grid position                      |
| Up Arrow    | Move cursor up one grid position                        |
| Down Arrow  | Move cursor down one grid position                      |

### Mouse Commands

|                      |                                                |
|----------------------|------------------------------------------------|
| Scroll Wheel         | Zoom in and out at the current cursor position |
| Ctrl + Scroll Wheel  | Pan right and left                             |
| Shift + Scroll Wheel | Pan up and down                                |
| Right Button Click   | Open context menu                              |

### Context Menu

Displayed by right-clicking the mouse:

- Add Line

- Add Rectangle

- Add Text

- Add Bitmap

- Zoom selection: direct selection of the display zoom.

- Grid selection: direct selection of the grid.

## Status Bar Information

The status bar is located at the bottom of the Drawing Sheet Editor and
provides useful information to the user.

<figure>
<img src="images/en/pl_status_bar.png" style="width:95.0%"
alt="pl status bar" />
</figure>

Coordinates are always relative to the corner selected as the reference
corner in the reference corner dropdown in the menubar.

# Properties editor

The right pane is a properties editor. It only appears when an item is
selected in the canvas. The **Item Properties** tab contains properties
for the selected item. These properties depend on what type of item is
selected. The **General Options** tab lets you edit default properties
and margins for the \*sheet.

Changes made in the properties editor are not applied until you click
the **Apply** button.

|                                                |                                                |
|------------------------------------------------|------------------------------------------------|
| ![item properties](images/item_properties.png) | ![general options](images/general_options.png) |

# Design Inspector window

The Design Inspector shows a table of every item in the drawing sheet
and their properties. Selecting an item in the Design Inspector also
selects the item on the canvas and leaves it selected when you close the
Design Inspector.

To open the Design Inspector, use **Inspect** → **Show Design
Inspector**.

<figure>
<img src="images/design_inspector.png" alt="design inspector" />
</figure>

# Interactive editing

## Item selection

An item can be selected:

- From the Design Inspector

- By Left clicking on it.

- By Right clicking on it (and a pop up menu will be displayed).

When selected, this item is drawn in a lighter shade of color and the
properties editor will be displayed for the selected item.

When right clicking on the item, a pop-up menu is displayed. The pop
menu contents depend on the type of object selected.

## Item creation

To add a new item, use the appropriate button in the right toolbar and
then click on the canvas. The item will be added to the canvas and
selected, and the properties editor for the new item will open. You can
edit the item’s properties in the properties editor, then click
**Apply** to modify the new item.

The available items are lines (![add graphical segments
24](images/icons/add_graphical_segments_24.png)), rectangles (![add
rectangle 24](images/icons/add_rectangle_24.png)), text (![text
24](images/icons/text_24.png)), and bitmaps (![image
24](images/icons/image_24.png)).

You can also add new items from the right-click context menu.

Logos must first be created by the Image Converter tool, which creates a
page layout description file. You can use the **Append Existing Drawing
Sheet** command to insert the logo (a poly polygon) contained in the new
drawing sheet.

## Adding lines, rectangles and text

When you add lines, rectangles, or text, the item will be added to the
canvas, selected, and shown in the properties editor. You can edit the
item’s properties in the properties editor, then click **Apply** to
apply the changes.

You can also move the item in the canvas after it has been placed by
dragging it or using the Move command (kbd:\[M\]). Lines and rectangles
can be moved as a shape, but you can also move their points
individually.

Lines and rectangles typically use the same corner reference for both
the start and end points. If this is not the case, the item’s geometry
will change when the sheet size or margins are changed.

## Adding logos

To add a logo, a poly polygon (the vectored image of the logo) must be
first created using the Image Converter tool, which is available in the
main KiCad Project Manager window. Polygons cannot be created by hand.

The Image Converter tool creates a drawing sheet file which contains
only one item: a poly polygon representing the source image. This
drawing sheet file is appended to the current design, using the **Append
Existing Drawing Sheet** command.

<div class="note">

This command can be used to append any drawing sheet file, regardless of
what items it contains. All items in the appended file will be added to
the current design.

</div>

Once a poly polygon is inserted, it can be moved and its parameters
edited.

## Adding image bitmaps

You can add an image bitmap using many common bitmap formats (PNG, JPEG,
BMP, etc.).

- When a bitmap is imported, its PPI (pixel per inch) definition is set
  to 300PPI.

- This value can be modified in the properties editor.

- The actual size of the bitmap in the drawing sheet depends on this
  parameter.

- Be aware that using higher definition values brings larger output
  files, and can have an effect on draw or plot time.

Bitmaps can be repeated but not rotated.
