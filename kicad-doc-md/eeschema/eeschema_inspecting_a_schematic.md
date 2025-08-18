# Inspecting a schematic

## Find tool

The Find tool searches for text in the schematic, including reference
designators, pin names, symbol fields, and graphic text. When the tool
finds a match, the canvas is zoomed and centered on the match and the
text is highlighted. Launch the tool using the ![Find
icon](images/icons/find_24.png) button in the top toolbar.

<figure>
<img src="images/en/find_dialog.png" style="width:50.0%"
alt="Find dialog" />
</figure>

The Find tool has several options:

- **Match case**: Selects whether the search is case-sensitive.

- **Whole words only**: When selected, the search will only match the
  search term with complete words in the schematic. When unselected, the
  search will match if the search term is part of a larger word in the
  schematic.

- **Regular Expression**: When selected, regular expressions can be used
  in the search terms.

- **Search pin names and numbers**: Selects whether the search should
  apply to pin names and numbers.

- **Search net names**: Selects whether the search should apply to net
  names (labels, symbol pins, sheet pins, and bus members).

- **Search hidden fields**: Selects whether the search should apply only
  to visible fields or if it should include hidden symbol fields.

- **Search the current sheet only**: Selects whether the search should
  be limited to the current schematic sheet.

- **Search the current selection only**: Selects whether the search
  should be limited to the current selection.

There is also a Find and Replace tool which is activated with the ![Find
and Replace icon](images/icons/find_replace_24.png) button in the top
toolbar. This tool behaves the same as the Find tool, but additionally
can replace some or all matches with different text.

<figure>
<img src="images/en/find_replace_dialog.png" style="width:50.0%"
alt="Find and Replace dialog" />
</figure>

The Find and Replace tool has the same options as the Find tool, with
one addition:

- **Replace matches in reference designators**: When selected, reference
  designators will be modified if they contain matching text. Otherwise
  reference designators will not be affected.

## Search panel

The search panel is a docked panel that lists information about symbols,
text, and labels from the schematic. Show or hide the search panel with
**View** → **Panels** → **Search** or use the
<span class="keycombo">Ctrl+G</span> shortcut.

<figure>
<img src="images/search_panel.png" style="width:80.0%"
alt="Search panel" />
</figure>

You can optionally filter the list based on a search string. When no
filter is used, all items in the design are listed in the corresponding
tab. Items from the entire schematic are listed, not just items in the
current sheet. Items are filtered based on their properties:

- Symbols and power symbols are filtered by the contents of their
  fields. You can select whether to search hidden fields by enabling the
  **Search Hidden Fields** option in the ![config
  16](images/icons/config_16.png) menu. Symbols are also filtered by
  their metadata (library link, description, and keywords) if **Search
  Metadata** is enabled in the ![config 16](images/icons/config_16.png)
  menu.

- Text (text and textboxes) is filtered by the text content.

- Labels are filtered by their netnames.

You can sort the filtered results in ascending or descending order of
the value in a particular column by clicking on that column header.

Filters support wildcards: `*` matches any characters, and `?` matches
any single character. You can also use [regular
expressions](http://docs.wxwidgets.org/3.2/overview_resyntax.html), such
as `/symbol value/`.

The displayed information depends on the item type:

- All items list their name and/or value, page number, and X/Y location
  in the sheet.

- Symbols additionally list their reference designator, footprint,
  attributes (Exclude from Simulation, Exclude from BOM, Exclude from
  Board, and Do Not Populate), library link (library name and symbol
  name), and description.

- Power symbols additionally list their reference designator.

- Text and labels additionally list their type, e.g. textbox or
  hierarchical.

When you click an item in the search panel, the schematic editor
switches to the item’s schematic sheet, and the item is selected in the
editing canvas. Depending on what is configured in the ![config
16](images/icons/config_16.png) menu, the schematic editor will also pan
and/or zoom to the selected item in the editing canvas. Double-clicking
an item in the search panel opens its properties dialog.

## Net highlighting

An electrical net can be highlighted in the schematic editor to
visualize all of the places it appears in the schematic. Net
highlighting can be activated in the Schematic Editor or by highlighting
the corresponding net in the PCB editor when cross-probe highlighting is
enabled (see below). When net highlighting is active, the highlighted
net will be shown in a different color. By default this color is pink,
but it is configurable in the Color section of the Preferences dialog.

Nets can be highlighted by clicking on a wire or pin using the Highlight
Net tool in the right toolbar (![Net Highlight
icon](images/icons/net_highlight_schematic_24.png)). Alternatively, the
Highlight Net hotkey (\`) highlights the net under the cursor.

Net highlighting can be cleared by using the Clear Net Highlight action
(hotkey ~) or by using the Highlight net tool on an empty region in the
schematic. By default, Esc also clears net highlighting, but this can be
disabled if desired in **Preferences** → **Schematic Editor** →
**Editing Options**.

## Net navigator

The net navigator is a docked panel that shows the location of every
occurrence of a highlighted net in a schematic. Show or hide the net
navigator with **View** → **Panels** → **Net Navigator**.

<figure>
<img src="images/net_navigator.png" alt="net navigator" />
</figure>

When you highlight a net in the schematic, every place where that net is
shown in the schematic is listed in the net navigator panel. All labels,
symbol pins, and sheet pins connected to the net are listed. Each
occurrence is sorted under its schematic sheet. Clicking on an
occurrence displays that item in the editing canvas.

When no net is highlighted, the net navigator displays this information
for all nets in the schematic.

<div class="note">

The net navigator displays [highlighted](#net-highlighting) nets, not
selected nets.

</div>

With the net navigator open and a net highlighted, you can quickly
select various items on that net (net labels, sheet pins, and symbol
pins) by selecting one of the items on the highlighted net in the
schematic canvas and pressing
Tab/<span class="keycombo">Shift+Tab</span> to cycle through the net
items. Pressing Tab selects the next item on the net, while
<span class="keycombo">Shift+Tab</span> selects the previous item.

## Cross-probing from the PCB

KiCad allows bi-directional cross-probing between the schematic and the
PCB. There are several different types of cross-probing.

**Selection cross-probing** allows you to select a symbol or pin in the
schematic to select the corresponding footprint or pad in the PCB (if
one exists) and vice-versa. By default, cross-probing will result in the
display centering on the cross-probed item and zooming to fit. You can
disable the centering and zooming behavior, or disable selection
cross-probing entirely, in the Display Options section of the
Preferences dialog. Even when selection cross-probing is disabled, you
can manually cross-probe from the schematic to the PCB by right-clicking
an object and selecting **Select on PCB**, or from the PCB to the
schematic by right-clicking an object **and choosing \*Select **→**
Select on Schematic**\*.

**Highlight cross-probing** allows you to highlight a net in the
schematic and PCB at the same time. If the option "Highlight
cross-probed nets" is enabled in the Display Options section of the
Preferences dialog, highlighting a net or bus in the schematic editor
will cause the corresponding net or nets to be highlighted in the PCB
editor, and vice versa.

## Electrical rules checking

The Electrical Rules Checker (ERC) tool checks for certain errors in
your schematic, such as unconnected pins, unconnected hierarchical
symbols, shorted outputs or other illegal connections, etc. ERC
violations are reported as errors or warnings depending on the severity
of the issue detected.

ERC is imperfect and cannot detect all errors, but it can detect many
common issues and oversights. All detected issues should be checked and
addressed before proceeding. The quality of the ERC is directly related
to the care taken in declaring [electrical pin
properties](#pin-electrical-types) during symbol creation. If symbols
are designed incorrectly, ERC will not report accurate information.

ERC can be started by clicking on the ![ERC
icon](images/icons/erc_24.png) button in the top toolbar and clicking
the **Run ERC** button.

<figure>
<img src="images/en/dialog_erc.png" style="width:70.0%"
alt="ERC dialog" />
</figure>

Any warnings or errors are reported in the **Violations** tab, and
markers for each violation are placed in the schematic so that they
point to the relevant part of the schematic. Warnings are indicated by
yellow arrows, and errors have red arrows. Excluded violations are shown
as green arrows. A list of the ignored tests are shown in the **Ignored
Tests** tab. A report file in plain text format can be created after
running DRC using the **Save…​** button.

<div class="note">

Selecting a violation in the ERC window jumps to the selected violation
marker in the schematic.

</div>

The numbers at the bottom of the window show the number of errors,
warnings, and exclusions. Each type of violation can be filtered from
the list using the respective checkboxes. Clicking **Delete Marker**
will clear the selected violation until ERC is run again, while clicking
**Delete All Markers** will clear all violations until the next ERC run.

Violations can be right-clicked in the dialog to ignore them or change
their severity:

<figure>
<img src="images/erc_ignore_warning.png" style="width:70.0%"
alt="Ignore ERC warning" />
</figure>

- **Exclude this violation:** ignores this particular violation, but
  does not affect any other violations. You can un-exclude a violation
  by right clicking the excluded violation and selecting **Remove
  exclusion for this violation**.

- **Exclude with comment…​:** the same as **Exclude this violation**, but
  prompts for a comment explaining the reason for the exclusion. When
  excluded violations are unhidden (using the **Exclusions** checkbox),
  exclusion comments are shown with the corresponding excluded
  violation. To edit an existing exclusion comment or add a comment to
  an existing exclusion, right click an excluded violation and select
  **Edit exclusion comment…​**.

- **Change severity:** changes a type of violation from warning to
  error, or error to warning. This affects all violations of a given
  type.

- **Ignore all:** ignores all violations of a given type. This test will
  now appear in the **Ignored Tests** tab rather than the **Violations**
  tab. You can un-ignore the test again by right clicking the test in
  the **Ignored Tests** tab, or in the Violation Severity panel in
  [Schematic Setup](#schematic-setup).

- \*Edit violation severities…​: opens the Violation severity panel in
  [Schematic Setup](#schematic-setup), for editing the severities of all
  DRC violation types.

You can also exclude the selected marker with **Inspect** → **Exclude
Marker**, and show or hide each category of marker (errors, warnings,
and exclusions) with the **View** menu.

Excluded and ignored violations are remembered between runs of the
design rule checker. Excluded violations are hidden unless the
**Exclusions** checkbox is enabled. Ignored violations are not shown,
but there is a list of ignored tests in the **Ignored Tests** tab.

### ERC example

<figure>
<img src="images/erc_pointers.png" style="width:70.0%"
alt="ERC pointers" />
</figure>

There are three errors in the screenshot above.

- Two outputs have been connected together (red arrow at right).

- Two inputs have been left unconnected (red arrows at left). This is
  actually two errors per pin: each pin is unconnected, and each pin is
  an input pin that is not driven by an output pin.

Selecting an ERC marker displays a description of the violation in the
message pane at the bottom of the window.

<figure>
<img src="images/erc_pointers_message.png" style="width:80.0%"
alt="ERC violation description in message pane" />
</figure>

### Per-net ERC markers

Some violations are reported only once per net. For example, the "Input
Power pin not driven by any Output Power pins" error technically applies
to each Input Power pin on an un-driven net, but only one marker is
shown per net. This is to avoid producing a large number of markers for
a single root cause. In this case, exactly where the marker is placed in
the schematic is arbitrary and does not necessarily indicate the ideal
location for fixing the issue.

In the example below, there are four un-driven Input Power pins (one per
power symbol), but only two ERC markers. Adding two `PWR_FLAG` symbols,
one to each un-driven net, will clear the errors. The "correct" location
for the `PWR_FLAG` symbol depends on the schematic and may not be near
the particular power symbol where the marker was placed, or even in the
same schematic sheet.

<figure>
<img src="images/eeschema_erc_single_marker.png" style="width:70.0%"
alt="Single marker for multiple violations on the same net" />
</figure>

### Power pins and power flags

It is common to have an "Input Power pin not driven by any Output Power
pins" error on power pins, as shown in the example below, even though
the power pins seem to be properly connected to a power rail. This
happens in designs where the power is provided through connectors or
other components that are not marked as power outputs. In these cases
ERC won’t detect any Output Power pins connected to the net and will
determine the Input Power pin is not driven by a power source.

For example, in the below schematic, `VCC` and `GND` are connected to a
connector with passive pins, so the Input Power pins of `U1` are not
considered driven by a power source.

<figure>
<img src="images/eeschema_power_pins_and_flags.png" style="width:70.0%"
alt="Power pins and error flags" />
</figure>

To avoid this warning, connect the net to `PWR_FLAG` symbol on such a
power net as shown in the following example. The `PWR_FLAG` symbol is
found in the `power` symbol library. Alternatively, connect any Output
Power pin to the net; `PWR_FLAG` is simply a symbol with a single Output
Power pin.

<figure>
<img src="images/eeschema_power_pins_and_flags_fixed.png"
style="width:70.0%" alt="Power pins with PWR_FLAGs" />
</figure>

Ground nets often need a `PWR_FLAG` to be manually added, even when
power rails do not, because voltage regulators have outputs declared as
Output Power, but their ground pins are typically marked as Power
Inputs. Therefore grounds are not considered driven by these voltage
regulators. This is so multiple regulators can share a common ground
without errors caused by two Output Power pins being connected together.

Power nets do not "jump" across components, so, components like passive
inline filtering elements may need a `PWR_FLAG` on their downstream side
to indicate that the net is still connected to a power source.

In the below schematic, although there is a `PWR_FLAG` on the nets
connected to the connectors (`VCC_IN` and `GND_IN`), a `PWR_FLAG` is
also needed to mark each of `VCC` and `GND` as power nets, because the
filter `FL1` has only Passive pins.

<figure>
<img src="images/eeschema_power_pins_and_flags_filter.png"
style="width:70.0%"
alt="Power pins with PWR_FLAG on both sides of a filter" />
</figure>

This would be the case also if the `VCC_IN` and `GND_IN` nets were
connected to a symbol with Output Power pins. In fact, in this
schematic, the `PWR_FLAG` symbols are on `VCC_IN` and `GND_IN` are not
required, as there are no Input Power pins on those nets.

For more information about power pins and power flags, see the
[`PWR_FLAG` documentation](#pwr-flag).

### ERC Configuration

The **Violation Severity** panel in [Schematic Setup](#schematic-setup)
lets you configure what types of ERC messages should be reported as
Errors, Warnings, or ignored.

<figure>
<img src="images/eeschema_erc_severity.png" style="width:70.0%"
alt="Schematic ERC severity settings" />
</figure>

The **Pin Conflicts Map** panel in [Schematic Setup](#schematic-setup)
allows you to configure connectivity rules to define electrical
conditions for errors and warnings based on what types of pins are
connected to each other. For example, by default an error is produced
when an output pin is connected to another output pin.

<figure>
<img src="images/eeschema_erc_options.png" style="width:70.0%"
alt="Schematic ERC Pin Conflicts Map" />
</figure>

Rules can be changed by clicking on the desired square of the matrix,
causing it to cycle through the choices: allowed, warning, error.

### List of ERC checks

The table below lists the electrical rules that KiCad checks and the
default violation severity for each check. All severities are
configurable.

#### Connections ERC checks

These ERC checks look for issues with wire and label connections in the
schematic.

| Violation                                                 | Description                                                                                                                                                                                                                                                                                                                                                       | Default Severity         |
|-----------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| Pin not connected                                         | This violation occurs when a symbol pin is not connected to a net, unless the pin has a [no-connect flag](#no-connection-symbols) or has electrical type Unconnected.                                                                                                                                                                                             | Error                    |
| Input pin not driven by any Output pins                   | This violation occurs when a symbol pin with electrical type Input is not connected to a driving pin. Driving pins are pins with the type output, bidirectional, tristate, power output, or passive pins.                                                                                                                                                         | Error                    |
| Input Power pin not driven by any Output Power pins       | This violation occurs when a symbol pin with electrical type Input Power is not connected to an Output Power pin. A common cause of this violation is [described above](#power-pins-and-power-flags).                                                                                                                                                             | Error                    |
| A pin with a "no connection" flag is connected            | The violation occurs when a symbol pin with a [no connection flag](#no-connection-symbols) is connected to a net.                                                                                                                                                                                                                                                 | Warning                  |
| Unconnected "no connection" flag                          | This violation occurs when a [No connection flag](#no-connection-symbols) is not connected to a pin or label.                                                                                                                                                                                                                                                     | Warning                  |
| Label not connected to anything                           | This violation occurs when a global, hierarchical, local, or directive label is not connected to a pin or another label.                                                                                                                                                                                                                                          | Error                    |
| Global label not connected anywhere else in the schematic | This violation occurs when there are fewer than two symbol pins on a net with a global label (if there are fewer than two pins, then the label isn’t being used to connect anything as it is only connected to a single symbol pin).                                                                                                                              | Warning                  |
| Global label only appears once in the schematic           | This violation occurs when a global label only appears once in the schematic, meaning that the label is not forming any global connections. This violation is ignored by default to allow users to use global labels to label nets, even if the net does not connect anywhere else.                                                                               | Ignore                   |
| Local and global labels have the same name                | This violation occurs when a local label has the same name as a global label. If these labels are on separate sheets, they will not connect, although they may have been intended to connect. Note that while a local label and a global label with the same name won’t connect if they are on different sheets, they will connect if they are on the same sheet. | Warning                  |
| Wires not connected to anything                           | This violation occurs when a wire is not connected to any pin or label.                                                                                                                                                                                                                                                                                           | Error                    |
| Bus Entry needed                                          | This violation only applies to projects imported from EAGLE projects. It indicates places where the importer was unable to automatically add bus entries to the imported schematic, so you must add them by hand.                                                                                                                                                 | Error                    |
| Symbol pin or wire end off connection grid                | This violation occurs when a symbol pin or wire end is not aligned to the connection grid. Symbol pins and wire ends need to be aligned to the grid in order to connect to each other. The grid used for this check is defined by the **connection grid** setting in [**Schematic Setup** → **Formatting** → **Connection grid**](#schematic-setup-formatting).   | Warning                  |
| Four connection points are joined together                | This violation occurs when wires join in a four-way (cross) junction. Such junctions are sometimes considered harmful because it can be unclear if all four wires are intended to be joined or if two wires were intended to cross without a junction.                                                                                                            | Ignore                   |
| Multiple pins with the same pin number                    | This violation occurs when two pins in the same symbol have the same pin number. Symbol pins must be uniquely numbered within a symbol, and therefore this violation is always an error.                                                                                                                                                                          | Error (not configurable) |
| Label connects more than one wire                         | This violation occurs when a label anchor connects to two wires (where two wires cross without connecting). In this situation is not possible to determine which net the label should connect to.                                                                                                                                                                 | Warning                  |
| Unconnected wire endpoint                                 | This violation occurs when a wire endpoint is not connected to anything.                                                                                                                                                                                                                                                                                          | Warning                  |

#### Conflicts ERC checks

These ERC checks look for conflicting information in symbols, sheets,
and buses.

| Violation                                                            | Description                                                                                                                                                                                                                                                           | Default Severity |
|----------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------|
| Duplicate reference designators                                      | This violation occurs when two symbols have the same reference designator.                                                                                                                                                                                            | Error            |
| Units of same symbol have different values                           | This violation occurs when units of a single symbol have different values.                                                                                                                                                                                            | Error            |
| Different footprint assigned in another unit of the symbol           | This violation occurs when units of a single symbol have different assigned footprints.                                                                                                                                                                               | Error            |
| Different net assigned to a shared pin in another unit of the symbol | This violation occurs when a pin that is shared between multiple units of a symbol is not connected to the same net in each unit.                                                                                                                                     | Error            |
| Duplicate sheet names within a given sheet                           | This violation occurs when two hierarchical sheets in the same parent sheet have the same name.                                                                                                                                                                       | Error            |
| Mismatch between hierarchical labels and sheet pins                  | This violation occurs when a hierarchical label does not have a corresponding hierarchical sheet pin in the parent sheet, or a hierarchical sheet pin does not have a corresponding hierarchical label in the child sheet.                                            | Error            |
| More than one name given given to this bus or net                    | This violation occurs when a net has multiple labels attached. Nets can only have a single name, so if multiple labels are attached to a net, one name will be selected and used as the canonical name.                                                               | Warning          |
| Conflict between bus alias definitions across schematic sheets       | This violation occurs when a bus alias has different members in different sheets. If the same bus alias name is used in multiple sheets, the members of the alias must be the same for each sheet.                                                                    | Error            |
| Buses are graphically connected but share no bus members             | This violation occurs when buses that are graphically connected do not have bus members in common.                                                                                                                                                                    | Error            |
| Invalid connection between bus and net items                         | This violation occurs when a bus is connected to a net item, such as a wire, a label referring to a single net, or a sheet pin referring to a single net. Labels and sheet pins can only be connected to buses if they refer to buses rather than individual signals. | Error            |
| Net is graphically connected to a bus but not a bus member           | This violation occurs when a net is connected to a bus with a bus entry but the net is not a member of that bus.                                                                                                                                                      | Warning          |

#### Miscellaneous ERC checks

These ERC checks look for other miscellaneous issues in the schematic.

<table>
<colgroup>
<col style="width: 30%" />
<col style="width: 50%" />
<col style="width: 20%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">Violation</th>
<th style="text-align: left;">Description</th>
<th style="text-align: left;">Default Severity</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p>Symbol is not annotated</p></td>
<td style="text-align: left;"><p>This violation occurs when a symbol is
not <a href="#reference-designators-and-symbol-annotation">annotated
with a unique reference designator</a>.</p></td>
<td style="text-align: left;"><p>Error</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>Unresolved text variable</p></td>
<td style="text-align: left;"><p>This violation occurs when a text
variable (<code>${variable_name}</code>) is used without being defined
in <a href="#schematic-setup-text-variables">Schematic
Setup</a>.</p></td>
<td style="text-align: left;"><p>Error</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>SPICE model issue</p></td>
<td style="text-align: left;"><p>This violation occurs when a SPICE
model has a syntax error or other problem.</p></td>
<td style="text-align: left;"><p>Ignore</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>Labels are similar (lower/upper case
difference only)</p></td>
<td style="text-align: left;"><p>This violation occurs when two labels
are similar and differ only by the case of some letters. This may be a
typo causing two labels to be disconnected when they are intended to be
connected.</p></td>
<td style="text-align: left;"><p>Warning</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>Power pins are similar (lower/upper
case difference only)</p></td>
<td style="text-align: left;"><p>This violation occurs when the net
names driven by two <a href="#power-symbols">global power pins</a> are
similar and differ only by the case of some letters. This may be a typo
causing two global power pins to be disconnected when they are intended
to be connected.</p></td>
<td style="text-align: left;"><p>Warning</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>Power pin and label are similar
(lower/upper case difference only)</p></td>
<td style="text-align: left;"><p>This violation occurs when a label and
the net name driven by a <a href="#power-symbols">global power pin</a>
are similar and differ only by the case of some letters. This may be a
typo causing two global power pins to be disconnected when they are
intended to be connected.</p></td>
<td style="text-align: left;"><p>Warning</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>Library symbol issue</p></td>
<td style="text-align: left;"><p>This violation occurs when one of
several symbol library issues is detected:</p>
<ul>
<li><p>The symbol library for a symbol is not included and enabled in
the <a href="#managing-symbol-libraries">library table</a></p></li>
<li><p>A symbol in the schematic does not exist in its symbol
library</p></li>
</ul></td>
<td style="text-align: left;"><p>Warning</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>Symbol doesn’t match copy in
library</p></td>
<td style="text-align: left;"><p>This violation occurs when a symbol in
the schematic is different than the library version of the symbol.</p>
<p>You can compare between the schematic and library versions of the
symbol using the <a href="#comparing-symbols">Compare Symbol with
Library</a> tool, which is available by right clicking the violation in
the ERC window. If desired, you can <a
href="#updating_and_exchanging_symbols">update the schematic symbol</a>
to match the library symbol.</p></td>
<td style="text-align: left;"><p>Warning</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>Footprint link issue</p></td>
<td style="text-align: left;"><p>This violation occurs when one of
several footprint assignment issues is detected:</p>
<ul>
<li><p>The footprint assignment for a symbol is not a valid footprint
identifier</p></li>
<li><p>The footprint library given in a symbol’s footprint assignment is
not included and enabled in the <a
href="../pcbnew/pcbnew.xml#managing-footprint-libraries">library
table</a></p></li>
<li><p>The footprint assigned to a symbol does not exist in the
specified footprint library</p></li>
</ul></td>
<td style="text-align: left;"><p>Warning</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>Assigned footprint doesn’t match
footprint filters</p></td>
<td style="text-align: left;"><p>This violation occurs when the
footprint assigned to a symbol does not match the symbol’s footprint
filters. If the symbol doesn’t have any footprint filters, no violation
occurs.</p></td>
<td style="text-align: left;"><p>Warning</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>Symbol has more units than are
defined</p></td>
<td style="text-align: left;"><p>This violation occurs when a symbol has
more units placed in the schematic than are defined in the symbol. Units
in the schematic must correspond exactly to the symbol
definition.</p></td>
<td style="text-align: left;"><p>Error</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>Symbol has units that are not
placed</p></td>
<td style="text-align: left;"><p>This violation occurs when a unit from
a multi-unit symbol is not placed in the schematic. Unplaced units will
not be connected to anything.</p></td>
<td style="text-align: left;"><p>Warning</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>Symbol has input pins that are not
placed</p></td>
<td style="text-align: left;"><p>This violation occurs when a multi-unit
symbol has units with input pins that are not placed, so those input
pins will not be connected to anything.</p></td>
<td style="text-align: left;"><p>Warning</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>Symbol has bidirectional pins that are
not placed</p></td>
<td style="text-align: left;"><p>This violation occurs when a multi-unit
symbol has units with bidirectional pins that are not placed, so those
input pins will not be connected to anything.</p></td>
<td style="text-align: left;"><p>Warning</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>Symbol has power input pins that are
not placed</p></td>
<td style="text-align: left;"><p>This violation occurs when a multi-unit
symbol has units with power input pins that are not placed, so those
input pins will not be connected to anything.</p></td>
<td style="text-align: left;"><p>Error</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>Conflict problem between pins</p></td>
<td style="text-align: left;"><p>This violation occurs when a connection
between pins is not allowed per the allowed connections in the <a
href="#erc-configuration">Pin Conflicts Map</a>.</p></td>
<td style="text-align: left;"><p>From Pin Conflicts Map</p></td>
</tr>
</tbody>
</table>

### User-definable ERC violations

You can manually trigger schematic ERC warnings or errors using special
[text variables](#text-variables). These items will appear as errors or
warnings when ERC runs. This can be useful to flag items for later
followup or review.

To cause an ERC violation, use the text variable
`${ERC_ERROR <violation name>}` or `${ERC_WARNING <violation name>}`
depending on whether an error or warning is desired. You can place this
in a text item, text box, or field, including symbol fields, sheet
fields, and label fields. When ERC runs, this will generate a ERC
violation with the given violation name. These text variables resolve to
an empty string in the schematic, and any text after the braces is
included in the ERC violation’s description. The text variable must be
placed at the start of the text object in order to trigger a violation.

For example, a text item containing
`${ERC_ERROR TODO}Calculate resistor value` will appear in the board as
just the text "Calculate resistor value", and will generate an ERC error
named "TODO" with "Calculate resistor value" in the description.

### ERC report file

An ERC report file can be generated and saved by clicking the **Save…​**
button in the ERC dialog. The file extension for ERC report files is
`.rpt`. An example ERC report file is given below.

    ERC report (Fri 21 Oct 2022 02:07:05 PM EDT, Encoding UTF8)

    ***** Sheet /
    [pin_not_driven]: Input pin not driven by any Output pins
        ; Severity: error
        @(149.86 mm, 60.96 mm): Symbol U1B [74LS00] Pin 4 [, Input, Line]
    [pin_not_connected]: Pin not connected
        ; Severity: error
        @(149.86 mm, 60.96 mm): Symbol U1B [74LS00] Pin 4 [, Input, Line]
    [pin_not_connected]: Pin not connected
        ; Severity: error
        @(149.86 mm, 66.04 mm): Symbol U1B [74LS00] Pin 5 [, Input, Line]
    [pin_to_pin]: Pins of type Output and Output are connected
        ; Severity: error
        @(165.10 mm, 63.50 mm): Symbol U1B [74LS00] Pin 6 [, Output, Inverted]
        @(165.10 mm, 46.99 mm): Symbol U1A [74LS00] Pin 3 [, Output, Inverted]
    [pin_not_driven]: Input pin not driven by any Output pins
        ; Severity: error
        @(149.86 mm, 66.04 mm): Symbol U1B [74LS00] Pin 5 [, Input, Line]

     ** ERC messages: 5  Errors 5  Warnings 0
