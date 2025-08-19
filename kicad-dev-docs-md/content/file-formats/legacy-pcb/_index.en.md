title: Legacy Board Format (pre 4.0) weight: 50 ---

<div class="formalpara-title">

**Summary The printed circuit board file format prior to version 4.0.**

</div>

\<!--more-→

<div class="warning">

This is for the PCB file format before the S expression format
introduced in KiCad 4.0. This documentation is a mostly verbatim copy of
what was written back then and the quality may be poor. This is only
included as a reference and is not maintained.

</div>

# Information about V1 version:

- Board files ( \*.brd files ) are in ASCII format.

- Dimensions are in 1/10000 inch, except for the page size (in 1/1000
  inch).

First line is something as: PCBNEW-BOARD Version 1 date 02/04/2011
15:04:20 All the following descriptions are like this:

    $DESCRIPTION
    some data
    …
    $endDESCRIPTION

When \<DESCRIPTION\> is an identifier which gives the meaning of the
data between \$DESCRIPTION and \$endDESCRIPTION.

**Example:**

    $GENERAL
    encoding utf-8
    LayerCount 2
    Ly 1FFF8001
    Links 66
    NoConn 0
    Di 24940 20675 73708 40323
    Ndraw 16
    Ntrack 267
    Nzone 1929
    Nmodule 29
    Nnets 26
    $EndGENERAL

    $SHEETDESCR
    Sheet A4 11700 8267
    Title ""
    Date "23 feb 2004"
    Rev ""
    Comp ""
    Comment1 ""
    Comment2 ""
    Comment3 ""
    Comment4 ""
    $EndSHEETDESCR

# Information about V2 version:

The file format is exactly the same format, and the extension is still
.brd. However, dimensions are in mm (floating notation), except for the
page size (in 1/1000 inch). Because the internal Pcbnew unit is now 1nm,
the integer coordinates in 1/10000 inch cannot be used in files. Of
course, the Pcbnew versions which are in nm are able to read the V1
version files, but can only write files in V2 version. The V2 version
should be seen as a temporary way to store boards without loss of
resolution.

First line is something as:

`PCBNEW-BOARD Version 2 date 22/02/2013 15:04:20`

All the following descriptions are like this:

    $DESCRIPTION
    some data
    …
    $endDESCRIPTION

Example:

    PCBNEW-BOARD Version 2 date 22/02/2013 10:33:30

    # Created by Pcbnew(2013-02-20 BZR 3963)-testing

    $GENERAL
    encoding utf-8
    Units mm
    LayerCount 2
    EnabledLayers 1FFF8001
    Links 200
    NoConn 0
    Di 69.241669 24.89454 202.336401 196.2404
    Ndraw 19
    Ntrack 779
    Nzone 0
    BoardThickness 1.6002
    Nmodule 25
    Nnets 111
    $EndGENERAL

# Information about new “S expression” version:

For Pcbnew versions in nanometers, the default file format is now using
“S expressions”. This new format uses mm for coordinates, fixes issues
(like spaces in names) in V1 and V2 versions, and is more human readable
than the older format. The new file extension is .kicad_pcb

Here is a sample:

    (kicad_pcb (version 3) (host pcbnew "(2013-01-12 BZR 3902)-testing")

      (general
        (links 200)
        (no_connects 0)
        (area 69.241669 24.89454 202.336401 196.2404)
        (thickness 1.6002)
        (drawings 19)
        (tracks 779)
        (zones 0)
        (modules 25)
        (nets 111)
      )

      (page A4)
      (title_block
        (title Demo)
        (rev 2.C)
        (company Kicad)
      )

# Layer numbering:

Tracks and other items (texts, drawings …​) use one layer. Pads and vias
use several layers. There are 16 copper layers and 13 technical layers.
The layer parameter used in descriptions has the value:

|         |                                                        |                    |
|---------|--------------------------------------------------------|--------------------|
| value   | layer name                                             |                    |
| 0       | Copper layer                                           | "Copper" layers    |
| 1 to 14 | Inner layers                                           |                    |
| 15      | Component layer                                        |                    |
| 16      | Copper side adhesive layer                             | "Technical" layers |
| 17      | Component side adhesive layer                          |                    |
| 18      | Copper side Solder paste layer                         |                    |
| 19      | Component Solder paste layer                           |                    |
| 20      | Copper side Silk screen layer                          |                    |
| 21      | Component Silk screen layer                            |                    |
| 22      | Copper side Solder mask layer                          |                    |
| 23      | Component Solder mask layer                            |                    |
| 24      | Draw layer (Used for general drawings)                 |                    |
| 25      | Comment layer (Other layer used for general drawings)  |                    |
| 26      | ECO1 layer (Other layer used for general drawings)     |                    |
| 27      | ECO2 layer (Other layer used for general drawings)     |                    |
| 28      | Edge layer. Items on Edge layer are seen on all layers |                    |
| 29      | Not yet used                                           |                    |
| 30      | Not yet used                                           |                    |
| 31      | Not yet used                                           |                    |

**Mask layer:** Sometimes, a mask layer parameter is used. It is a 32
bits mask used to indicate a layer group usage (0 up to 32 layers). A
mask layer parameter is given in hexadecimal form. Bit 0 is the copper
layer, bit 1 is the inner 1 layer, and so on…​(Bit 27 is the Edge layer).
Mask layer is the ORed mask of the used layers

# First line of description:

**Format:**

`PCBNEW-BOARD Version <version number> date <date>-<time>`

Date and time are useful only for information (not used by pcbnew).

# \$GENERAL

This data is useful only when loading file. It is used by Pcbnew for
displaying activity when loading data.

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p>$GENERAL</p></td>
<td style="text-align: left;"><p>Start description</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>Ly 1FFF8001</p></td>
<td style="text-align: left;"><p>Obsolete (used for old pcbnew
compatibility)</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>Links 66</p></td>
<td style="text-align: left;"><p>Total number of connections</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>NoConn 0</p></td>
<td style="text-align: left;"><p>Remaining connections</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>Di 24940 20675 73708 40323</p></td>
<td style="text-align: left;"><p>Bounding box coordinates:<br />
X_start Y_start X_end Y_end</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>Ndraw 16</p></td>
<td style="text-align: left;"><p>Number of draw items like edge
segments, texts…​</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>Ntrack 267</p></td>
<td style="text-align: left;"><p>Number of track segments</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>Nzone 1929</p></td>
<td style="text-align: left;"><p>Number of zone segments</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>Nmodule 29</p></td>
<td style="text-align: left;"><p>Number of modules</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>Nnets 26</p></td>
<td style="text-align: left;"><p>Number of nets</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>$EndGENERAL</p></td>
<td style="text-align: left;"><p>End description</p></td>
</tr>
</tbody>
</table>

# \$SHEETDESCR

This the page size and texts.

|                     |                                                   |
|---------------------|---------------------------------------------------|
| \$SHEETDESCR        | Start description                                 |
| Sheet A4 11700 8267 | \<Page size\> X_size Y_size in mils (1/1000 inch) |
| Title ""            | Title text                                        |
| Date "23 feb 2004"  | Date text                                         |
| Rev ""              | Revision text                                     |
| Comp ""             | Company name text                                 |
| Comment1 ""         | Comment text, line 1                              |
| Comment2 ""         | Comment text, line 2                              |
| Comment3 ""         | Comment text, line 3                              |
| Comment4 ""         | Comment text, line 4                              |
| \$EndSHEETDESCR     | End description                                   |

# \$SETUP block:

This data bock is used for design settings This is useful only for board
edition. Example:

    $SETUP
    InternalUnit 0.000100 INCH
    Layers 2
    Layer[0] Cuivre signal
    Layer[15] Composant signal
    TrackWidth 250
    TrackWidthHistory 25
    TrackWidthHistory 170
    TrackWidthHistory 250
    TrackClearence 110
    ZoneClearence 150
    DrawSegmWidth 150
    EdgeSegmWidth 50
    ViaSize 600
    ViaDrill 250
    ViaSizeHistory 600
    MicroViaSize 200
    MicroViaDrill 80
    MicroViasAllowed 0
    TextPcbWidth 170
    TextPcbSize 600 800
    EdgeModWidth 150
    TextModSize 600 600
    TextModWidth 120
    PadSize 1500 2500
    PadDrill 1200
    AuxiliaryAxisOrg 29500 55500
    $EndSETUP

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p>$SETUP</p></td>
<td style="text-align: left;"><p>Start block "SETUP"</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>InternalUnit 0.000100 INCH</p></td>
<td style="text-align: left;"><p>Internal unit for Pcbnew, all
coordinates are in this unit</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>Layers 2</p></td>
<td style="text-align: left;"><p>Number of layers (2 = double sided
board) must be 1 to 16</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>Layer[0] Cuivre signal</p></td>
<td style="text-align: left;"><p>layer name and type<br />
name = name given to the layer by the user (here: "cuivre"<br />
type = signal (not current used in Pcbnew)</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>Layer[15] Composant signal</p></td>
<td style="text-align: left;"></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>TrackWidth 250</p></td>
<td style="text-align: left;"><p>Current track width</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>TrackWidthHistory 170</p></td>
<td style="text-align: left;"><p>Last used track widths</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>TrackWidthHistory 250</p></td>
<td></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>TrackWidthHistory 400</p></td>
<td></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>TrackClearence 100</p></td>
<td style="text-align: left;"><p>Isolation for DRC (Design rules
check)</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>ZoneClearence 200</p></td>
<td style="text-align: left;"><p>Isolation used in zone filling</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>DrawSegmWidth 120</p></td>
<td style="text-align: left;"><p>Current segment width for drawings on
technical layers</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>EdgeSegmWidth 120</p></td>
<td style="text-align: left;"><p>Current segment width for drawings on
"edge layer"</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>ViaSize 700</p></td>
<td style="text-align: left;"><p>Current via size</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>ViaDrill 250</p></td>
<td style="text-align: left;"><p>Via drill for this board</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>ViaSizeHistory 450</p></td>
<td style="text-align: left;"><p>Last used via sizes</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>ViaSizeHistory 650</p></td>
<td></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>ViaSizeHistory 700</p></td>
<td></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>TextPcbWidth 120</p></td>
<td style="text-align: left;"><p>Current text width for texts on copper
or technical layers. This is not for text on footprints</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>TextPcbSize 600 600</p></td>
<td style="text-align: left;"><p>Current text X Y size</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>EdgeModWidth 120</p></td>
<td style="text-align: left;"><p>Current Segment width for footprint
edition</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>TextModSize 120 600</p></td>
<td style="text-align: left;"><p>Current text XY size for texts for
footprint edition</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>TextModWidth 120</p></td>
<td style="text-align: left;"><p>Current text width for texts for
footprint edition</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>PadSize 700 700</p></td>
<td style="text-align: left;"><p>Current X Y pad size (footprint
edition)</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>PadDrill 320</p></td>
<td style="text-align: left;"><p>Current pad drill</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>AuxiliaryAxisOrg 0 0</p></td>
<td style="text-align: left;"><p>Auxiliary axis position<br />
(Auxiliary axis is the reference coordinate (0 0 coordinate) for
EXCELLON drilling files</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>$EndSETUP</p></td>
<td style="text-align: left;"><p>End block "SETUP"</p></td>
</tr>
</tbody>
</table>

# \$EQUIPOT

\$EQUIPOT describes a net name.

|                 |                                         |
|-----------------|-----------------------------------------|
| \$EQUIPOT       | Start block                             |
| Na 2 "N-000026" | Na \<internal net number\> « net name » |
| St ~            |                                         |
| \$EndEQUIPOT    | End block                               |

Note1: Internal net number is an arbitrary number. It is computed by
Pcbnew when compiling netlist. Note2: Net 0 is not a real net. Net 0 is
the net number used internally by Pcbnew for all the no connected pads.

Example:

    $EQUIPOT;
    Na 0 ""
    St ~
    $EndEQUIPOT
    $EQUIPOT
    Na 1 "DONE"
    St ~
    $EndEQUIPOT
    $EQUIPOT
    Na 2 "N-000026"
    St ~
    $EndEQUIPOT
    $EQUIPOT
    Na 3 "TD0/PROG"
    St ~
    $EndEQUIPOT

# \$MODULE

Description start by: `` $MODULE <module name>` ``

And ends with `` $EndMODULE <module name>` ``

Module description has four sections:

1.  General description (fixed size)

2.  Field description (variable size)

3.  Drawing description (variable size)

4.  Pad description. (variable size)

5.  3D shape information.

**Note:** All coordinates are relative to the module position. Its means
the coordinates of segments, pads, texts …​ are given for a module in
position 0, rotation 0. If a module is rotated or mirrored, real
coordinates must be computed according to the real position and
rotation.

## General description:

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p>$MODULE bornier6</p></td>
<td style="text-align: left;"><p>$MODULE &lt;module lib
name&gt;</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>Po 62000 30500 2700 15 3EC0C28A
3EBF830C ~~</p></td>
<td style="text-align: left;"><p>Po Xpos Ypos Orientation(0.1deg) Layer
TimeStamp Attribut1Attribut2<br />
Attribut1 = ~or 'F' for autoplace (F = Fixed, ~= moveable)<br />
Attribut2 = ~or 'P' for autoplace (P = autoplaced)</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>Li bornier6</p></td>
<td style="text-align: left;"><p>Li &lt;module lib name&gt;</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>Cd Bornier d’alimentation 4
pins</p></td>
<td style="text-align: left;"><p>Cd comment description (displayed when
browsing libraries)</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>Kw DEV</p></td>
<td style="text-align: left;"><p>Kw Keyword1 Keyword2 …​ (for footprint
selection by keywords)</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>Sc 3EBF830C</p></td>
<td style="text-align: left;"><p>Sc TimeStampOp</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>Op 0 0 0</p></td>
<td style="text-align: left;"><p>Op &lt;rotation cost 90 deg&gt;
&lt;rotation cost 180 deg&gt; for auto place.<br />
rotation cost = 0 (no rotation allowed) to 10 (null cost)</p></td>
</tr>
</tbody>
</table>

**Note:** Usually, components are on layer 15 (component layer) or 0
(copper layer). If the component is on layer 0, it is"mirrored". The
"mirror axis is the X axis

## Field Description:

There are 2 to 12 fields

Field 0 = component reference (U1, R5 …​) (required) Field 1 = component
value (10K, 74LS02 …​) (required) Other fields (optional) are comments.
Format:

`` T<field number> <Xpos> <Ypos> <Xsize> <Ysize> <rotation> <penWidth> N <visible> <layer> "text"` ``

|              |                               |                                                                 |
|--------------|-------------------------------|-----------------------------------------------------------------|
| Field        | Units                         | Meaning                                                         |
| field number | enumeration                   | 0⇒reference, 1⇒value, etc.                                      |
| Xpos         | tenths of mils (.0001 inches) | The horizontal offset relative to the module’s overall position |
| Ypos         | tenths of mils (.0001 inches) | The vertical offset relative to the module’s overall position   |
| Xsize        | tenths of mils (.0001 inches) | The horizontal size of the character 'M'                        |
| Ysize        | tenths of mils (.0001 inches) | The vertical size of the character 'M'                          |
| rotation     | tenths of degrees             | Angular rotation from horizontal, counterclockwise              |
| penWidth     | tenths of mils (.0001 inches) | Width of the pen used to draw characters                        |
| N            | none                          | flag for the parser?                                            |
| visible      | boolean                       | I⇒ invisible, V⇒ visible                                        |
| layer        | enumeration                   | see layer numbers above                                         |

Examples:

|                                             |                |
|---------------------------------------------|----------------|
| T0 500 -3000 1030 629 2700 120 N V 21 "P1"  | T0 ⇒ reference |
| T1 0 3000 1201 825 2700 120 N V 21 "CONN_6" | T1 ⇒ value     |

## Drawings:

Tells how to draw module shape. They cannot be on a copper layer (DRC
ignore them) Drawings are segment, circle, arc, polygon.

### Draw segment:

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p>DS -6000 -1500 -6000 1500 120
21</p></td>
<td style="text-align: left;"><p>DS is a Draw Segment<br />
DS Xstart Ystart Xend Yend Width Layer</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>DS 6000 1500 6000 -1500 120 21</p></td>
<td style="text-align: left;"><p>An other Draw Segment</p></td>
</tr>
</tbody>
</table>

### Circle:

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p>DC Xcentre Ycentre Xpoint Ypoint Width
Layer</p></td>
<td style="text-align: left;"><p>DC is a Draw Circle<br />
Xpoint, Ypoint is a point on the circle.</p></td>
</tr>
</tbody>
</table>

### Arc:

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p>DA Xcentre Ycentre Xstart_point
Ystart_point angle width layer</p></td>
<td style="text-align: left;"><p>DA is a Draw Arc<br />
angle is the arc angle in 0.1 degrees</p></td>
</tr>
</tbody>
</table>

### Polygon:

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p>DP 0 0 0 0 corners_count width
layer</p></td>
<td style="text-align: left;"><p>Draw Polygon<br />
First line of a polygon.<br />
The polygon should be closed, otherwise this is a poly-line.<br />
width is the thickness of outlines.</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>Dl corner_posx corner_posy</p></td>
<td style="text-align: left;"><p>Corner coordinate<br />
( corners_count lines like this)</p></td>
</tr>
</tbody>
</table>

### Pad Descriptions:

All the pads of this footprint are listed here (Many \$PAD/\$EndPAD
sections here).. See \$PAD description.

### \$SHAPE3D

3D shape information: The real shape description is a vrml file, build
by Wings3d. This shape can be scaled, moved and rotated. This is because
a single 3D shape can be used for many footprints (for instance, we use
the shape resistor.wrl for several resistor footprints, by tuning the X,
Y, Z scale of the 3D shape according to the different size of resistor
footprints). Some smd footprints are using this feature. For the same
reasons, the 3D shape can be moved (by the move factor) and/or rotated.
**Real shape unit is 0.1 inch (1 unit vrml = 0.1 inch = 2.54
millimeter).** An other reason exists: when a footprint is very big ( a
big connector) or very small (a small SMD resistor) we must create a 3D
shape small or bigger than real size, in order to use easily the 3D
modeler.

|                               |                                                      |
|-------------------------------|------------------------------------------------------|
| \$SHAPE3D                     | Start description                                    |
| Na "device/bornier_6.wrl"     | FileName (default path is kicad/modules/packages3d/) |
| Sc 1.000000 1.000000 1.000000 | X Y Z scale factor                                   |
| Of 0.000000 0.000000 0.000000 | X Y Z offset (move vector, in 3D units (0.1 inch))   |
| Ro 0.000000 0.000000 0.000000 | X Y Z rotation (in degree)                           |
| \$EndSHAPE3D                  | End description                                      |

The 3D shape coordinates are relative to the footprint coordinates. The
3D shape must be scale, moved and rotated according to the parameters Sc
Of and Ro, and after moved and rotated according to the footprint
coordinates and rotation. If the footprint is « inverted » (that is,
located on copper side) the 3D shape must be « inverted » too.

<div class="note">

A footprint may have several 3D shapes (for instance an integrated
circuit and his socket).

</div>

### \$PAD

Pads have different shapes and attributes. Pad shapes are: \* Circle. \*
Oblong(or oval). \* Rectangular (Square is like a rectangle). \*
Trapeze.

Pad attributes are:

- Normal (Has usually a hole)

- Smd (used for Surface Mounted Devices). Has no hole.

- Connector (used for connectors like a PC Board Bus connector)

- Mechanical. (Like a hole for mechanical use)

And shape can be draw with an offset related to the drilling hole. The
hole shale is round or oblong

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p>$PAD</p></td>
<td style="text-align: left;"><p>Start description</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>Sh "2" C 1500 1500 0 0 2700</p></td>
<td style="text-align: left;"><p>Shape: &lt;pad name&gt; shape Xsize
Ysize Xdelta Ydelta Orientation</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>Dr 600 0 0<br />
or (oblong hole)<br />
Dr 600 0 0 O 600 650</p></td>
<td style="text-align: left;"><p>Drill &lt;Pad drill&gt; Xoffset Yoffset
(round hole)<br />
or (oblong hole)<br />
Drill &lt;Pad drill.x&gt; Xoffset Yoffset &lt;Hole shape&gt; &lt;Pad
drill.x&gt; &lt;Pad drill.y&gt;</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>At STD N 00E0FFFF</p></td>
<td style="text-align: left;"><p>Attributs: &lt;Pad type&gt; N &lt;layer
mask&gt;</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>Ne 8 "GND"</p></td>
<td style="text-align: left;"><p>Net reference of the pad:
&lt;netnumber&gt; &lt;net name&gt;</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>Po -3000 0</p></td>
<td style="text-align: left;"><p>X_pos Y_pos (relative to the module
position)</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>$EndPAD</p></td>
<td style="text-align: left;"><p>End description</p></td>
</tr>
</tbody>
</table>

Note: \<Pad type\> is the Pad Attribute. It is one of: "STD" "SMD"
"CONN" "HOLE" "MECA". Shape is one of:

- C (circle)

- R (Rectangular).

- O (Oblong)

- T (Trapèze) Hole shape = O (O for Oblong)

Example:

    $PAD
    Sh "3" C 1500 1500 0 0 2700
    Dr 600 0 0
    At STD N 00E0FFFF
    Ne 10 "TD0_1"
    Po -1000 0
    $EndPAD

# Graphic items:

There are drawing items like segments, circles, texts, targets and
cotations.

## \$DRAWSEGMENT

Draw segments are:

- segments (strait line)

- circles

- arcs

### Line:

|                                  |                                               |
|----------------------------------|-----------------------------------------------|
| \$DRAWSEGMENT                    | Start description                             |
| Po 0 67500 39000 65500 39000 120 | Position shape Xstart Ystart Xend Yend width  |
| De 28 0 900 0 0                  | Description layer type angle timestamp status |
| \$EndDRAWSEGMENT                 | End description                               |

Note:

- shape = 0

- Angle is used only for arc segments (unused for line, left for
  compatibility).

### Circle:

|                                  |                                                |
|----------------------------------|------------------------------------------------|
| \$DRAWSEGMENT                    | Start description                              |
| Po 1 67500 39000 65500 39000 120 | Position shape Xcentre Ycentre Xend Yend width |
| De 28 0 900 0 0                  | Description layer type angle timestamp status  |
| \$EndDRAWSEGMENT                 | End description                                |

Note: \* shape = 1 \* Angle is used only for arc segments (unused for
circle, left for compatibility). \* End is a point of this circle. (If
Xend or Yend is 0, the other coordinate is the radius)

### Arc:

|                                  |                                               |
|----------------------------------|-----------------------------------------------|
| \$DRAWSEGMENT                    | Start description                             |
| Po 2 67500 39000 65500 39000 120 | Position shape Xstart Ystart Xend Yend width  |
| De 28 0 900 0 0                  | Description layer type angle timestamp status |
| \$EndDRAWSEGMENT                 | End description                               |

Note:

- shape = 2

- start and end are the 2 points of the arc. angle is the arc angle (in
  0.1 degree). Center coordinates are computed by pcbnew from start, end
  and angle.

Currently, only 90 degrees arcs are supported.(thereby, angle = 900)

Example:

    $DRAWSEGMENT
    Po 0 67500 34000 67500 39000 120
    De 28 0 900 0
    $EndDRAWSEGMENT

## \$TEXTPCB

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p>$TEXTPCB</p></td>
<td style="text-align: left;"><p>Start description</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>Te "TDI"</p></td>
<td style="text-align: left;"><p>Text "string"</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>Po 57250 35750 600 600 150 0</p></td>
<td style="text-align: left;"><p>Position Xstart Ystart Xsize Ysize
Width rotation</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>De 15 1 B98C Normal</p></td>
<td style="text-align: left;"><p>Description layer normal timestamp
style<br />
normal = 0 : text is mirrored.<br />
normal = 1 : text is normal.<br />
style = Normal or Italic</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>$EndTEXTPCB</p></td>
<td style="text-align: left;"><p>End description</p></td>
</tr>
</tbody>
</table>

Example:

    $TEXTPCB
    Te "TCK"
    Po 57250 33500 600 600 150 0
    De 15 1 B98C Normal
    $EndTEXTPCB

## \$MIRE

     shape 1
    shape 0

|                                       |                                               |
|---------------------------------------|-----------------------------------------------|
| \$MIREPCB                             | Start description                             |
| Po 0 28 28000 51000 5000 150 00000000 | Position shape Xpos Ypos size width timestamp |
| \$EndMIREPCB                          | End description                               |

## \$COTATION

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p>$COTATION</p></td>
<td style="text-align: left;"><p>Start description</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>Ge 0 24 0</p></td>
<td style="text-align: left;"><p>General shape layer timestamp<br />
currently, shape = 0.</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>Te "4,5500''"</p></td>
<td style="text-align: left;"><p>Text "string"<br />
string is the cotation value in inches or millimeters</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>Po 50250 5791 600 800 170 0 1</p></td>
<td style="text-align: left;"><p>Position (for text) Xpos Ypos Xsize
Ysize width orient normal</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>Sb 0 27500 6501 73000 6501 150</p></td>
<td style="text-align: left;"><p>Coordinates of segments (axis,
arrows…​)</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>Sd 0 73000 9000 73000 5081 150</p></td>
<td></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>Sg 0 27500 9000 27500 5081 150</p></td>
<td></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>S1 0 73000 6501 72557 6731 150</p></td>
<td></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>S2 0 73000 6501 72557 6271 150</p></td>
<td></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>S3 0 27500 6501 27943 6731 150</p></td>
<td></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>S4 0 27500 6501 27943 6271 150</p></td>
<td></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>$EndCOTATION</p></td>
<td style="text-align: left;"><p>End description</p></td>
</tr>
</tbody>
</table>

# Track, vias and Zone section:

## \$TRACK

Track section decribes tracks and vias on copper layers. Each track (or
via) has a two line description: For a track segment:

**Po**sition shape Xstart Ystart Xend Yend width  
**De**scription layer 0 netcode timestamp status Shape parameter is set
to 0 (reserved for future changes).

For a via: **Po**sition shape Xstart Ystart Xend Yend diameter  
**De**scription layer 1 netcode timestamp status

For a via, layer parameter gives : On the 4 less significant bits: the
starting layer of the via  
On the 4 next bits: the ending layer.

For instance, a via starting at copper kayer (layer 0) end ending at
component layer (layer 15 has the layer parameter set to F0 hexadecimal
or 240 decimal. + Shape parameter is the via type (through = 3, blind =
2, buried = 1) + Timestamp parameters are set to 0 (reserved for future
changes).  
Status parameter can be set to 0 (Used internally for routing
information).

<table>
<colgroup>
<col style="width: 50%" />
<col style="width: 50%" />
</colgroup>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p>$TRACK</p></td>
<td style="text-align: left;"><p>Start description</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>Po 0 36750 37000 36550 37000
250</p></td>
<td style="text-align: left;"><p>Position shape Xstart Ystart Xend Yend
width width = diameter for a via</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>De 15 0 1 0 400</p></td>
<td style="text-align: left;"><p>Description layer type netcode
timestamp status<br />
type = 0 for a track segment.<br />
type = 1 for a via</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>Po 0 39000 36750 38750 37000
250</p></td>
<td style="text-align: left;"><p>An other track</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>De 15 0 1 0 0</p></td>
<td></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>Po 3 53500 27000 53500 27000
650</p></td>
<td style="text-align: left;"><p>This is a via (via "through") from
layer 15 (component) to layer 0 (copper)</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>De 15 1 14 0 0</p></td>
<td></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>$EndTRACK</p></td>
<td style="text-align: left;"><p>End description</p></td>
</tr>
</tbody>
</table>

## \$ZONE

Zone section is like track section. (There is no via in Zone section).
It is used to handle a zone filling, from a zone outline.

|                                  |                           |
|----------------------------------|---------------------------|
| \$ZONE                           | Start description         |
| Po 0 67100 33700 67100 38600 100 | Same as track description |
| De 0 0 2 3EDDB09D 0              |                           |
| \$EndZONE                        | End description           |

## \$CZONE_OUTLINE

Describes the main outlines of a zone and the outlines of filled areas
(solid polygons) inside the zone main outlines. Outlines of filled areas
can be missing (if the zone is not currently filled). Because a zone
handles thermal reliefs, there are options to describe pads in zones
options and thermal reliefs parameters.

Example:

    $CZONE_OUTLINE
    ZInfo 47868246 1 "GND"
    ZLayer 0
    ZAux 4 E
    ZClearance 150 T
    ZMinThickness 190
    ZOptions 0 32 F 200 200
    ZCorner 74750 51750 0
    ZCorner 74750 13250 0
    ZCorner 29750 13250 0
    ZCorner 29750 51750 1
    ....
    $POLYSCORNERS
    74655 51655 0 0
    74655 13345 0 0
    ...
    $endPOLYSCORNERS
    $endCZONE_OUTLINE

|                                                                                    |                                                                                                    |
|------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| \$CZONE_OUTLINE                                                                    | Start description                                                                                  |
| ZInfo 478E3FC8 1 "/aux_sheet/INPUT" \<Time stamp\> \<internal netcode\> "net name" | ZLayer 0 Layer (0 = copper, 15 = component, 1 ..14 = inner layers)                                 |
| ZAux 4 E \<corners count\> \<zone hatching option\>                                | zone hatching option = N (none), E (edge hatching) or F (full hatching)                            |
| ZClearance 200 T \<Zone clearance\> \<pads option = I, T or X\>                    | I = pads in zone                                                                                   |
| T = Thermal reliefs                                                                | X = pads not in zone.                                                                              |
| ZMinThickness 190 \<Zone min thickness (for copper zone)\>                         | ZOptions 0 32 F 200 200 \<fill mode\> \<arc approx\> \<antipad thickness\> \<thermal stubs width\> |
| fill mode = 0 (use solid polygons) or 1 (use segments)                             | arc approx = 16 or 32 (segments count to approximate a 360 arc)                                    |
| ZCorner 49450 19150 0 First corner (external outline)                              | ZCorner 40600 19150 0 Next corner                                                                  |
| ZCorner 40600 22850 0                                                              | ZCorner 49450 22850 1 End corner (flag = 1)                                                        |
| \$POLYSCORNERS Start of filled areas outlines                                      | 74655 51655 0 0 First corner (first filled area outline)                                           |
| 74655 13345 0 0 Next corner                                                        | \$endPOLYSCORNERS                                                                                  |
| \$endCZONE_OUTLINE                                                                 | End description                                                                                    |

Other example:

    $CZONE_OUTLINE   Start description of an other outline
    ZInfo 47B3E800 3 "VCC"
    ZLayer 1
    ZAux 8 F
    ZClearance 200 T
    ZMinThickness 190   Zone min thickness (for copper zone)
    ZOptions 0 32 F 200 200
    ZCorner 49704 23032 0   First corner (external outline)
    ZCorner 49704 18940 0
    ZCorner 46140 19024 0
    ZCorner 46148 20000 0
    ZCorner 45250 20000 0
    ZCorner 44750 21250 0
    ZCorner 43750 22250 0
    ZCorner 46176 23068 1   End corner (flag = 1)
    ZCorner 48450 19900 0   First corner (this is a hole)
    ZCorner 48450 20800 0
    ZCorner 47350 20800 0
    ZCorner 47250 19900 1   End corner (flag = 1)
    $endCZONE_OUTLINE   End description

## \$EndBOARD

\$EndBOARD terminates the whole board description. Must be the last
line.
