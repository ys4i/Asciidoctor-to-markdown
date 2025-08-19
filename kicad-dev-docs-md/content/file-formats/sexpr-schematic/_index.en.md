title: Schematic File Format weight: 45 ---

<div class="formalpara-title">

**Summary The s-expression schematic file format.**

</div>

\<!--more-→

# Introduction

This documents the s-expression schematic file format for all versions
of KiCad from 6.0.

- Schematic files use the `.kicad_sch` extension.

## Instance Path

Because KiCad schematics can support multiple instances of the same
schematic using hierarchical sheets, information for shared sheets is
done using paths consisting of the [universally unique
identifiers](../sexpr-intro/index.xml#_universally_unique_identifier)
that represent the hierarchical path for the sheet the instance
separated by a forward slash ('/'). A typical instance path would look
like:

    "/00000000-0000-0000-0000-00004b3a13a4/00000000-0000-0000-0000-00004b617b88"

<div class="warning">

The first identifier must be the root
[sheet](#_hierarchical_sheet_section) which is the same identifier as
the root schematic file.

</div>

## Label and Pin Shapes

The table below defines the valid shape tokens [global
labels](#_global_label_section), [hierarchical
labels](#_hierarchical_label_section), and [hierarchical sheet
pins](#_hierarchical_sheet_pin_definition).

| Token         | Definition                            | Image                                                                     |
|---------------|---------------------------------------|---------------------------------------------------------------------------|
| input         | Label or pin is an input shape        | ![images/label_shape_input](images/label-shape-input.png)                 |
| output        | Label or pin is an output shape       | ![images/label_shape_output](images/label-shape-output.png)               |
| bidirectional | Label or pin is a bidirectional shape | ![images/label_shape_bidirectional](images/label-shape-bidirectional.png) |
| tri_state     | Label or pin is a tri-state shape     | ![images/label_shape_tristate](images/label-shape-tristate.png)           |
| passive       | Label or pin is a tri-state shape     | ![images/label_shape_passive](images/label-shape-passive.png)             |

# Layout

A schematic file includes the following sections:

- [Header](#_header_section)

- [Unique Identifier](#_unique_identifier_section)

- [Page Settings](../sexpr-intro/index.xml#_page_settings)

- [Title Block Section](../sexpr-intro/index.xml#_title_block)

- [Symbol Library Symbol Definition](#_library_symbol_section)

- [Junction Section](#_junction_section)

- [No Connect Section](#_no_connect_section)

- [Wire and Bus Section](#_wire_and_bus_section)

- [Image Section](#_image_section)

- [Graphical Line Section](#_graphical_line_section)

- [Graphical Text Section](#_graphical_text_section)

- [Local Label Section](#_local_label_section)

- [Global Label Section](#_global_label_section)

- [Symbol Section](#_symbol_section)

- [Hierarchical Sheet Section](#_hierarchical_sheet_section)

- [Root Sheet Instance Section](#_root_sheet_instance_section)

# Header Section

The `kicad_sch` token indicates that it is KiCad schematic file. This
section is required.

<div class="note">

Third party scripts should not use `eeschema` as the generator
identifier. Please use some other identifier so that bugs introduced by
third party generators are not confused with a schematic file created by
KiCad.

</div>

    (kicad_sch
      (version VERSION)                                             
      (generator GENERATOR)                                         

      ;; contents of the schematic file...                          
    )

- The `version` token attribute defines the schematic version using the
  YYYYMMDD date format.

- The `generator` token attribute defines the program used to write the
  file.

- The schematic sections go here.

# Unique Identifier Section

The `uuid` token defines the globally unique identifier that identifies
the schematic.

      UNIQUE_IDENTIFIER                                             

NOTE Only the root schematic identifier is used as the virtual root
sheet identifier. All other identifiers are belong to [hierarchical
sheet objects](#_hierarchical_sheet_section).

- The UNIQUE_IDENTIFIER defines the [universally unique
  identifier](../sexpr-intro/index.xml#_universally_unique_identifier)
  for the schematic file. This identifier is used when creating
  hierarchical sheet paths which are used to reference symbol instance
  data and [hierarchical sheet
  instance](#_hierarchical_sheet_instance_section) information.

# Library Symbol Section

The `lib_symbols` token defines a symbol library contain all of the
symbols used in the schematic.

      (lib_symbols
        SYMBOL_DEFINITIONS...                                       
      )

- A list of 0 or more [symbols](../sexpr-intro/index.xml#_symbols).

# Junction Section

The `junction` token defines a junction in the schematic. The junction
section will not exist if there are no junctions in the schematic.

      (junction
        POSITION_IDENTIFIER                                         
        (diameter DIAMETER)                                         
        (color R G B A)                                             
        UNIQUE_IDENTIFIER                                           
      )

- The POSITION_IDENTIFIER defines the [X and Y
  coordinates](../sexpr-intro/index.xml#_position_identifier) of the
  junction.

- The `diameter` token attribute defines the DIAMETER of the junction. A
  diameter of 0 is the default diameter in the system settings.

- The `color` token attributes define the Red, Green, Blue, and Alpha
  transparency of the junction. If all four attributes are 0, the
  default junction color is used.

- The UNIQUE_IDENTIFIER defines the [universally unique
  identifier](../sexpr-intro/index.xml#_universally_unique_identifier)
  for the junction.

# No Connect Section

The `no_connect` token defines a unused pin connection in the schematic.
The no connect section will not exist if there are not any no connects
in the schematic.

      (no_connect
        POSITION_IDENTIFIER                                         
        UNIQUE_IDENTIFIER                                           
      )

- The POSITION_IDENTIFIER defines the [X and Y
  coordinates](../sexpr-intro/index.xml#_position_identifier) of the no
  connect.

- The UNIQUE_IDENTIFIER defines the [universally unique
  identifier](../sexpr-intro/index.xml#_universally_unique_identifier)
  for the no connect.

# Bus Entry Section

The `bus_entry` token defines a bus entry in the schematic. The bus
entry section will not exist if there are no bus entries in the
schematic.

      (bus_entry
        POSITION_IDENTIFIER                                         
        (size X Y)                                                  
        STROKE_DEFINITION                                           
        UNIQUE_IDENTIFIER                                           
      )

- The POSITION_IDENTIFIER defines the [X and Y
  coordinates](../sexpr-intro/index.xml#_position_identifier) of the bus
  entry.

- The `size` token attributes define the X and Y distance of the end
  point from the position of the bus entry.

- The STROKE_DEFINITION defines how the bus entry [is
  drawn](../sexpr-intro/index.xml#_stroke_definition).

- The UNIQUE_IDENTIFIER defines the [universally unique
  identifier](../sexpr-intro/index.xml#_universally_unique_identifier)
  for the bus entry.

# Wire and Bus Section

The `wire` and `bus` tokens define wires and buses in the schematic.
This section will not exist if there are no wires or buses in the
schematic.

      (wire | bus
        COORDINATE_POINT_LIST                                       
        STROKE_DEFINITION                                           
        UNIQUE_IDENTIFIER                                           
      )

- The COORDINATE_POINT_LIST defines the list of [X and Y
  coordinates](../sexpr-intro/index.xml#_coordinate_point_list) of start
  and end points of the wire or bus.

- The STROKE_DEFINITION defines how the wire or bus [is
  drawn](../sexpr-intro/index.xml#_stroke_definition).

- The UNIQUE_IDENTIFIER defines the [universally unique
  identifier](../sexpr-intro/index.xml#_universally_unique_identifier)
  for the wire or bus.

# Image Section

See common [Images](../sexpr-intro/index.xml#_images) section.

# Graphical Line Section

The `polyline` token defines one or more lines that may or may not
represent a polygon. This section will not exist if there are no lines
in the schematic.

      (polyline
        COORDINATE_POINT_LIST                                       
        STROKE_DEFINITION                                           
        UNIQUE_IDENTIFIER                                           
      )

- The COORDINATE_POINT_LIST defines the list of [X/Y
  coordinates](../sexpr-intro/index.xml#_coordinate_point_list) of to
  draw line(s) between. A minimum of two points is required.

- The STROKE_DEFINITION defines how the graphical line [is
  drawn](../sexpr-intro/index.xml#_stroke_definition)..

- The UNIQUE_IDENTIFIER defines the [universally unique
  identifier](../sexpr-intro/index.xml#_universally_unique_identifier)
  for the graphical line.

# Graphical Text Section

The `text` token defines graphical text in a schematic.

      (text
        "TEXT"                                                      
        POSITION_IDENTIFIER                                         
        TEXT_EFFECTS                                                
        UNIQUE_IDENTIFIER                                           
      )

- The TEXT is a quoted string that defines the text.

- The POSITION_IDENTIFIER defines the [X and Y coordinates and rotation
  angle](../sexpr-intro/index.xml#_position_identifier) of the text.

- The TEXT_EFFECTS section defines how the [text is
  drawn](../sexpr-intro/index.xml#_text_effects).

- The UNIQUE_IDENTIFIER defines the [universally unique
  identifier](../sexpr-intro/index.xml#_universally_unique_identifier)
  for the graphical text.

# Local Label Section

The `label` token defines an wire or bus label name in a schematic.

      (label
        "TEXT"                                                      
        POSITION_IDENTIFIER                                         
        TEXT_EFFECTS                                                
        UNIQUE_IDENTIFIER                                           
      )

- The TEXT is a quoted string that defines the label.

- The POSITION_IDENTIFIER defines the [X and Y coordinates and rotation
  angle](../sexpr-intro/index.xml#_position_identifier) of the label.

- The TEXT_EFFECTS section defines how the [label text is
  drawn](../sexpr-intro/index.xml#_text_effects).

- The UNIQUE_IDENTIFIER defines the [universally unique
  identifier](../sexpr-intro/index.xml#_universally_unique_identifier)
  for the label.

# Global Label Section

The `global_label` token defines a label name that is visible across all
schematics in a design. This section will not exist if no global labels
are defined in the schematic.

      (global_label
        "TEXT"                                                      
        (shape SHAPE)                                               
        [(fields_autoplaced)]                                       
        POSITION_IDENTIFIER                                         
        TEXT_EFFECTS                                                
        UNIQUE_IDENTIFIER                                           
        PROPERTIES                                                  
      )

- The TEXT is a quoted string that defines the global label.

- The `shape` token attribute defines the way the global label is drawn.
  See table below for global label shapes.

- The optional `fields_autoplaced` is a flag that indicates that any
  PROPERTIES associated with the global label have been place
  automatically.

- The POSITION_IDENTIFIER defines the [X and Y coordinates and rotation
  angle](../sexpr-intro/index.xml#_position_identifier) of the label.

- The TEXT_EFFECTS section defines how the [global label text is
  drawn](../sexpr-intro/index.xml#_text_effects).

- The UNIQUE_IDENTIFIER defines the [universally unique
  identifier](../sexpr-intro/index.xml#_universally_unique_identifier)
  for the global label.

- The PROPERTIES section defines the
  [properties](../sexpr-intro/index.xml#_symbol_property) of the global
  label. Currently, the only supported property is the inter-sheet
  reference.

# Hierarchical Label Section

The `hierarchical_label` section defines labels that are used by
[hierarchical sheets](#_hierarchical_sheet_section) to define
connections between sheet in hierarchical designs. This section will not
exist if no global labels are defined in the schematic.

      (hierarchical_label
        "TEXT"                                                      
        (shape SHAPE)                                               
        POSITION_IDENTIFIER                                         
        TEXT_EFFECTS                                                
        UNIQUE_IDENTIFIER                                           
      )

- The TEXT is a quoted string that defines the hierarchical label.

- The `shape` token attribute defines the way the hierarchical label is
  drawn. See table below for hierarchical label shapes.

- The POSITION_IDENTIFIER defines the [X and Y coordinates and rotation
  angle](../sexpr-intro/index.xml#_position_identifier) of the label.

- The TEXT_EFFECTS section defines how the [hierarchical label text is
  drawn](../sexpr-intro/index.xml#_text_effects).

- The UNIQUE_IDENTIFIER defines the [universally unique
  identifier](../sexpr-intro/index.xml#_universally_unique_identifier)
  for the hierarchical label.

# Symbol Section

The `symbol` token in the symbol section of the schematic defines an
instance of a symbol from [the library symbol
section](#_library_symbol_section) of the schematic.

      (symbol
        "LIBRARY_IDENTIFIER"                                        
        POSITION_IDENTIFIER                                         
        (unit UNIT)                                                 
        (in_bom yes|no)                                             
        (on_board yes|no)                                           
        UNIQUE_IDENTIFIER                                           
        PROPERTIES                                                  
        (pin "1" (uuid e148648c-6605-4af1-832a-31eaf808c2f8))       
        (instances                                                  
          (project "PROJECT_NAME"                                   
            (path "PATH_INSTANCE"                                   
              (reference "REFERENCE")                               
              (unit UNIT)                                           
            )

            ;; Optional symbol instances for this `project`...
          )

          ;; Optional symbol instances for other `project`...
        )
      )

- The [LIBRARY_IDENTIFIER](../sexpr-intro/index.xml#_library_identifier)
  defines which symbol in the [library symbol
  section](#_library_symbol_section) of the schematic that this
  schematic symbol references.

- The POSITION_IDENTIFIER defines the [X and Y coordinates and angle of
  rotation](../sexpr-intro/index.xml#_position_identifier) of the
  symbol.

- The `unit` token attribute defines which unit in the symbol library
  definition that the schematic symbol represents.

- The `in_bom` token attribute determines whether the schematic symbol
  appears in any bill of materials output.

- The `on_board` token attribute determines if the footprint associated
  with the symbol is exported to the board via the netlist.

- The UNIQUE_IDENTIFIER defines the [universally unique
  identifier](../sexpr-intro/index.xml#_universally_unique_identifier)
  for the symbol. This is used to map the symbol the [symbol instance
  information](#_symbol_instance_section).

- The PROPERTIES section defines a list of [symbol
  properties](../sexpr-intro/index.xml#_symbol_property) of the
  schematic symbol.

- The `pin` token attributes define ???.

- The `instances` token defines a list of symbol instances grouped by
  project. Every symbol will have a least one instance.

- The `project` token attribute defines the name of the project to which
  the instance data belongs. There can be instance data from other
  project when schematics are shared across multiple projects. The
  projects will be sorted by the `PROJECT_NAME` in alphabetical order.

- The `path` token attribute is the [path to the sheet
  instance](#_instance_path) for the instance data.

- The `reference` token attribute is a string that defines the reference
  designator for the symbol instance.

- The `unit` token attribute is a integer ordinal that defines the
  symbol unit for the symbol instance. For symbols that do not define
  multiple units, this will always be 1.

# Hierarchical Sheet Section

The `sheet` token defines a hierarchical sheet of the schematic.

      (sheet
        POSITION_IDENTIFIER                                         
        (size WIDTH HEIGHT)                                         
        [(fields_autoplaced)]                                       
        STROKE_DEFINITION                                           
        FILL_DEFINITION                                             
        UNIQUE_IDENTIFIER                                           
        SHEET_NAME_PROPERTY                                         
        FILE_NAME_PROPERTY                                          
        HIERARCHICAL_PINS                                           
        (instances                                                  
          (project "PROJECT_NAME"                                   
            (path "PATH_INSTANCE"                                   
              (page "PAGE_NUMBER")                                  
            )

            ;; Optional sheet instances for this `project`...
          )

          ;; Optional sheet instances for other `project`...
        )
      )

- The POSITION_IDENTIFIER defines the [X and Y coordinates and angle of
  rotation](../sexpr-intro/index.xml#_position_identifier) of the sheet
  in the schematic.

- The `size` token attributes define the WIDTH and HEIGHT of the sheet.

- The optional `fields_autoplaced` token indicates if the properties
  have been automatically placed.

- The STROKE_DEFINITION defines how the sheet [outline is
  drawn](../sexpr-intro/index.xml#_stroke_definition).

- The FILL_DEFINITION defines how the sheet is
  [filled](../sexpr-intro/index.xml#_fill_definition).

- The UNIQUE_IDENTIFIER defines the [universally unique
  identifier](../sexpr-intro/index.xml#_universally_unique_identifier)
  for the sheet. This is used to map the sheet [symbol instance
  information](#_symbol_instance_section) and [sheet instance
  information](#_hierarchical_sheet_instance_section).

- The SHEET_PROPERTY_NAME is a
  [property](../sexpr-intro/index.xml#_symbol_property) that defines the
  name of the sheet. This property is mandatory.

- The FILE_NAME_PROPERTY is a
  [property](../sexpr-intro/index.xml#_symbol_property) that defines the
  file name of the sheet. This property is mandatory.

- The HIERARCHICAL_PINS section is a list of [hierarchical
  pins](#_hierarchical_sheet_pin_definition) that map a [hierarchical
  label](#_hierarchical_label_section) defined in the associated
  schematic file.

- The `instances` token defines a list of sheet instances grouped by
  project. Every sheet will have a least one instance.

- The `project` token attribute defines the name of the project to which
  the instance data belongs. There can be instance data from other
  project when schematics are shared across multiple projects. The
  projects will be sorted by the `PROJECT_NAME` in alphabetical order.

- The `path` token attribute is the [path to the sheet
  instance](#_instance_path) for the sheet instance data.

- The `page` token attribute is a string that defines the page number
  for the sheet instance.

## Hierarchical Sheet Pin Definition

The `pin` token in a [sheet](#_hierarchical_sheet_section) object
defines an electrical connection between the sheet in a schematic with
the [hierarchical label](#_hierarchical_label_section) defined in the
associated schematic file.

      (pin
        "NAME"                                                      
        input | output | bidirectional | tri_state | passive        
        POSITION_IDENTIFIER                                         
        TEXT_EFFECTS                                                
        UNIQUE_IDENTIFIER                                           
      )

- The "NAME" attribute defines the name of the sheet pin. It must have
  an identically named [hierarchical
  label](#_hierarchical_label_section) in the associated schematic file.

- The electrical connect type token defines the type of electrical
  connect made by the sheet pin.

- The POSITION_IDENTIFIER defines the [X and Y coordinates and angle of
  rotation](../sexpr-intro/index.xml#_position_identifier) of the pin in
  the sheet.

- The TEXT_EFFECTS section defines how the [pin name text is
  drawn](../sexpr-intro/index.xml#_text_effects).

- The UNIQUE_IDENTIFIER defines the [universally unique
  identifier](../sexpr-intro/index.xml#_universally_unique_identifier)
  for the pin.

# Root Sheet Instance Section

     (path
        "/"                                                         
        (page "PAGE")                                               
      )

- The instance path is always empty ("/") since there are no sheets
  pointing to the root sheet.

- The `page` token defines the page number of the root sheet. Page
  numbers can be any valid string.
