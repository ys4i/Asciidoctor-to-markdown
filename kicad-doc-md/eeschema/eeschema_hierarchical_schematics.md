# Hierarchical schematics

## Introduction

In KiCad, multi-sheet schematics are hierarchical: there is a single
root sheet, and additional sheets are created as subsheets of either the
root sheet or another subsheet. Sheets can be included in a hierarchy
multiple times, if desired.

Carefully drawing a schematic as a hierarchical design improves
schematic legibility and reduces repetitive drawing.

Creating a hierarchical schematic starts from the root sheet. The
process is to create a subsheet, then draw the circuit in the subsheet
and make the necessary electrical connections between sheets.
Connections can be made between nets in a subsheet and nets in the
parent sheet using hierarchical pins and labels, or between any two nets
in the hierarchy using global labels.

## Adding sheets to a design

You can add a subsheet to a design with the Add Hierarchical Sheet tool
(S hotkey, or the ![Add Hierarchical Sheet
icon](images/icons/add_hierarchical_subsheet_24.png) button in the right
toolbar). Launch the tool, then click twice in the canvas to draw the
upper left and lower right corners of the subsheet symbol. Make the
sheet outline large enough to fit the [hierarchical pins you will add
later](#hierarchical-sheet-pins).

<figure>
<img src="images/draw_hierarchical_sheet.png"
alt="Drawing a hierarchical sheet" />
</figure>

The Sheet Properties dialog will appear and prompt you for a sheet name
and filename.

<figure>
<img src="images/hierarchical_sheet_properties.png" style="width:70.0%"
alt="Hierarchical sheet properties" />
</figure>

The **sheet name** must be unique, as it is used in the full net name
for any nets in the subsheet. For example, a net with the local label
`net1` in the sheet `sheet1` would have a full net name of
`/sheet1/net1`. The sheet name is also used to refer to the sheet in
various places in the GUI, including the [title
block](#sheet-title-block) and the [hierarchy
navigator](#navigating-between-sheets). The sheet name can also be
changed in the hierarchy navigator.

The **sheet file** specifies the file that the new sheet will be saved
to or loaded from. The path to the sheet file can be relative or
absolute. It is usually preferable to save subsheet files in the project
directory and use a relative path so that the project is portable.

A single sheet file can be used more than once in a project by
specifying the same filename for each repeated sheet; the circuit drawn
in the sheet will be instantiated once per usage, and any edits in once
instance will be reflected in the other instances.

<div class="note">

Sheet files can be shared between multiple projects to allow design
reuse between projects. However, this is not recommended due to path
portability concerns and the risk of unintentionally changing other
projects while editing a shared sheet.

</div>

The sheet’s **page number** is configurable here. The page number is
displayed in the sheet [title block](#sheet-title-block) and the
[hierarchy navigator](#navigating-between-sheets), and sheets are sorted
by page number in the hierarchy navigator and when [printing or
plotting](#generating-outputs). The sheet number can also be changed in
the hierarchy navigator or for the current page with **Edit** → **Edit
Sheet Page Number…​**.

Other attributes for sheets are **Exclude from simulation**, **Exclude
from bill of materials**, **Exclude from board**, and **Do not
populate**. When any of these attributes are set for a sheet, they are
inherited by all symbols in that sheet, [as if they were set on the
symbols themselves](#editing-symbol-properties).

Several graphical options are also available. **Border width** sets the
width of the border around the sheet shape. **Border color** and
**Background fill** set the color for the border and fill of the sheet
shape, respectively. If no color is set, a checkerboard swatch is shown
and the default values from the color theme are used.

Sheets support arbitrary custom fields, which can be added and removed
with the ![plus icon](images/icons/small_plus_16.png) and ![trash
icon](images/icons/small_trash_16.png) buttons, respectively. Sheet
fields can be optionally displayed on the schematic by checking their
**Show** box, and they can be accessed from inside the sheet or in other
sheet fields using [text variables](#text-variables).

The Sheet Properties dialog can be accessed at any time by selecting a
sheet symbol and using the E hotkey, or by right-clicking on a sheet
symbol and selecting **Properties…​**.

## Navigating between sheets

You can enter a hierarchical sheet from the parent sheet by
double-clicking the child sheet’s shape, or right-clicking the child
sheet and selecting **Enter Sheet**.

Return to the parent sheet by using the ![up arrow
icon](images/icons/up_24.png) button in the top toolbar, or by
right-clicking in an empty part of the schematic and clicking **Leave
Sheet**.

You can jump to the next sheet with the ![right arrow
icon](images/icons/right_24.png) button, or to the previous sheet with
the ![left arrow icon](images/icons/left_24.png) button.

Alternatively, you can jump to any sheet with the hierarchy navigator.
To open the hierarchy navigator, click the ![Hierarchy navigator
icon](images/icons/hierarchy_nav_24.png) button in the left toolbar. The
hierarchy navigator docks at the left of the screen. Each sheet in the
design is displayed as an item in the tree. Clicking a sheet name opens
that sheet in the editing canvas. You can also use the hierarchy
navigator to rename or renumber a sheet by right clicking on the sheet
name and selecting **Edit page number** or **Rename**.

<figure>
<img src="images/hierarchy_navigator_pane.png" style="width:50.0%"
alt="hierarchy navigator" />
</figure>

## Electrical connections between sheets

### Label overview

Electrical connections between sheets are made with [labels](#labels).
There are several kinds of labels in KiCad, each with a different
connection scope.

- **Local labels** only make connections within a sheet. Therefore local
  labels cannot be used to connect between sheets. Local labels are
  added with the ![Local Label icon](images/icons/add_label_24.png)
  button.

- **Global labels** make connections anywhere in a schematic, regardless
  of sheet. Global labels are added with the ![Global Label
  icon](images/icons/add_glabel_24.png) button.

- **Hierarchical labels** connect to **hierarchical sheet pins**
  accessible in the parent sheet. Hierarchical designs rely on
  hierarchical labels and pins to make connections between parent sheets
  and child sheets. You can think of sheet pins as defining the
  interface for a sheet; hierarchical labels within the child sheet
  connect to corresponding sheet pins which are visible in the parent
  sheet. Hierarchical labels are added inside a child sheet using the
  ![Hierarchical Label icon](images/icons/add_hierarchical_label_24.png)
  button.

<div class="note">

Labels that have the same name will connect, regardless of the label
type, if they are in the same sheet.

</div>

<div class="note">

[Hidden power pins](#hidden-power-pins) can also be considered global
labels, because they connect anywhere in the schematic hierarchy.

</div>

### Hierarchical sheet pins

After placing hierarchical labels within the subsheet, matching
**hierarchical sheet pins** can be added to the subsheet symbol in the
parent sheet. You can then make connections to the hierarchical pins
with wires, labels, and buses. Hierarchical sheet pins in a subsheet
symbol are connected to the matching hierarchical labels in the subsheet
itself.

<div class="note">

Hierarchical labels must be defined in the subsheet before the
corresponding hierarchical sheet pin can be imported in the sheet
symbol.

</div>

<figure>
<img src="images/hierarchical_sheet_pins.png" style="width:70.0%"
alt="hierarchical sheet symbol with pins" />
</figure>

For every hierarchical label in the subsheet, add the corresponding
hierarchical pin onto the sheet symbol by clicking the ![Place Sheet
Pins icon](images/icons/add_hierar_pin_24.png) button in the right
toolbar, then clicking on the sheet symbol. A sheet pin for the first
unmatched hierarchical label will be attached to the cursor, where it
can be placed anywhere along the border of the sheet symbol. Clicking
again with the tool will continue to add additional sheet pins until all
of the hierarchical labels in the subsheet have a matching sheet pin on
the sheet symbol. Sheet pins can also be imported by selecting **Place
Sheet Pin** in a sheet symbol’s right-click context menu.

You can edit the properties of a sheet pin in the Sheet Pin Properties
dialog. Open this dialog by double-clicking a sheet pin, selecting a
sheet pin and using the E hotkey, or right-clicking a sheet pin and
selecting **Properties…​**.

<figure>
<img src="images/sheet_pin_properties.png"
alt="Sheet Pin Properties dialog" />
</figure>

The sheet pin’s **name** can be edited in the textbox or by selecting
from the dropdown list of hierarchical labels in the subsheet. A sheet
pin’s name has to match the corresponding hierarchical label in the
subsheet, so if you change a pin name you must change the label name as
well.

**Shape** changes the shape of the sheet pin, and has no electrical
effect. It can be set to Input, Output, Bidirectional, Tri-state, or
Passive. The pin’s **font**, **text size**, **color**, and emphasis
(bold or italic) can also be changed.

### Syncing sheet pins

Another way to manage the connections between hierarchical labels and
sheet pins is to use the Sync Sheet Pins tool. Launch this tool using
the ![Sync Sheet Pins
icon](images/icons/import_hierarchical_label_24.png) button in the right
toolbar or with **Sync Sheet Pins** in a sheet symbol’s right click
context menu.

<figure>
<img src="images/sync_sheet_pins.png" alt="sync sheet pins" />
</figure>

This dialog shows the hierarchical labels and hierarchical sheet pins
for each hierarchical sheet. If the tool was launched from the context
menu of a sheet symbol, only one tab will be available, with the labels
and sheet pins for that specific sheet. If the tool was started
globally, i.e. with the ![import hierarchical label
24](images/icons/import_hierarchical_label_24.png) button or with
**Place** → **Sync Sheet Pins**, a tab will be shown for each
hierarchical sheet.

The icon in each tab indicates whether the hierarchical sheet pins on
the sheet symbol are correctly matched with the hierarchical labels
inside the sheet. If the tab has a ![ercwarn
16](images/icons/ercwarn_16.png) icon, then there is a hierarchical
label in the sheet without a matching sheet pin, or a sheet pin without
a corresponding hierarchical label, or both. If the tab has a ![erc
green 16](images/icons/erc_green_16.png) icon, then the hierarchical
labels and hierarchical sheet pins are matched up correctly. Sheet pins
and labels are considered matching if they have the same name and the
same graphic shape (input, output, bidirectional, tri-state, or
passive).

The column on the left lists sheet pins for the current sheet that do
not have a corresponding hierarchical label in the sheet. The middle
column lists hierarchical labels in the current sheet that do not have a
corresponding hierarchical sheet pin on the sheet symbol. The right
column lists pairs of matching sheet pins and hierarchical labels. The
name of each pin or label is shown along with its graphic shape.

If you click the **Add Hierarchical Labels** button, new hierarchical
labels corresponding to the selected sheet pins will be created for you
to place sequentially in the sheet. The selected sheet pins are then
removed from the left column and added to to the right column for
matching sheet pins and labels. Clicking **Delete Sheet Pins** will
delete the selected sheet pins from the sheet symbol.

If you click the **Add Sheet Pins** button, new sheet pins corresponding
to the selected hierarchical labels will be created for you to place on
the sheet symbol. The hierarchical labels are then removed from the
middle column and added to the right column for matching sheet pins and
labels. Clicking **Delete Hierarchical Labels** will delete the selected
hierarchical labels from inside the sheet.

Clicking the ![add hierarchical label
16](images/icons/add_hierarchical_label_16.png) button will match the
selected sheet pin and hierarchical label by renaming the sheet pin to
match the hierarchical label’s name. Clicking the ![add hierar pin
16](images/icons/add_hierar_pin_16.png) button will do the opposite,
matching the selected sheet pin and hierarchical label by renaming the
label to match the sheet pin.

Clicking the ![left 16](images/icons/left_16.png) button will unmatch a
matched pair, moving both the sheet pin and the hierarchical label back
to their respective unmatched columns. The unmatched sheet pin and
hierarchical label can then be edited or rematched as desired.

Any changes made in the Sync Sheet Pins dialog are applied immediately,
before the dialog is closed. To cancel a change made in the Sync Sheet
Pins dialog, use Undo.

## Hierarchical design examples

Hierarchical designs can be put into one of several categories:

- **Simple:** each sheet is used only once.

- **Complex:** some sheets are instantiated multiple times.

- **Flat:** a sub-case of a **simple** hierarchy, without connections
  between subsheets and their parent. Flat hierarchies can be used to
  represent a non-hierarchical design.

Each hierarchy model can be useful; the most appropriate one depends on
the design.

### Simple hierarchy

An example of a simple hierarchy is the `video` demo project included
with KiCad. The root sheet contains seven unique subsheets, each with
hierarchical labels and sheet pins linking the sheets to each other in
the root sheet. Two of the subsheet symbols are shown below.

<figure>
<img src="images/eeschema_simple_hierarchy.png" style="width:80.0%"
alt="Simple hierarchy from video demo project" />
</figure>

### Complex Hierarchy

The `complex_hierarchy` demo project is an example of a complex
hierarchy. The root sheet contains two subsheet symbols, which both
refer to the same sheet file (`ampli_ht.kicad_sch`). This allows the
design to include two copies of the same amplifier circuit. Although the
two sheet symbols refer to the same filename, the sheet names are unique
(`ampli_ht_vertical` and `ampli_ht_horizontal`). Inside each subsheet
the circuits are identical except for the reference designators, which
as always are unique.

This project contains no sheet pin connections. The only connections
between the root sheet and the subsheets are global power connections
made with [power symbols](#power-symbols). However, sheets in a complex
hierarchy could include sheet pin connections if appropriate for the
design.

<figure>
<img src="images/eeschema_complex_hierarchy.png" style="width:80.0%"
alt="Complex hierarchy from complex_hierarchy demo project" />
</figure>

### Flat hierarchy

The `flat_hierarchy` demo project is an example of a flat hierarchy. The
root sheet contains two unique subsheet symbols with no hierarchical
sheet pins. The root sheet in this project does nothing except hold the
subsheets, and the subsheets are used only as additional pages in the
schematic.

<div class="note">

This is the simplest way to create multi-page schematics in KiCad.

</div>

<figure>
<img src="images/eeschema_flat_hierarchy.png" style="width:80.0%"
alt="Flat hierarchy from flat_hierarchy demo project" />
</figure>
