title: User Interface Policy weight: 10 summary: Rules on how the user
interface elements should be implemented. ---

# Introduction

This document defines the guidelines for user interface development in
KiCad. Developers are expected to following these guidelines as closely
as possible when contributing user interface code to the KiCad project.

# Follow platform-specific guidelines

Except for the cases listed below, KiCad follows the platform-specific
user interface guidelines for each supported platform on that platform.
This means there may be differences in UI behavior/look-and-feel between
the platforms where their guidelines differ.

The user interface guidelines for each supported platform are:

- GNOME (GTK): <https://developer.gnome.org/hig/stable/>

- macOS:
  <https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/>

- Windows:
  <https://docs.microsoft.com/en-us/windows/win32/uxguide/guidelines>

<div class="important">

UI behavior that is inconsistent with the platform default or that would
override platform defaults/theme settings must be avoided.

</div>

# Text and strings

## Text Capitalization

For all visible text used within KiCad, follow recommendations in the
capitalization section in the [writing style section of the GNOME User
Interface
Guidelines](https://developer.gnome.org/hig/guidelines/writing-style.html).
This applies to all Menus, Titles, Labels, Tooltips, Buttons, etc.

The capitalization for the application names is KiCad, Eeschema, CvPcb,
GerbView, and Pcbnew. All strings that have application names that are
visible to the user should be capitalized this way. It’s also a good
idea use this capitalization in source code comments as well to prevent
confusion of new contributors.

## Capitalization Styles

There are two styles of capitalization used in the user interface
elements: header capitalization and sentence capitalization. This
section defines the capitalization style and when each type of
capitalization should be used.

### Header Capitalization

When using header capitalization all words are capitalized with the
following exceptions:

- Articles: a, an, the.

- Conjunctions: and, but, for, not, so, yet …​

- Prepositions of three or fewer letters: at, for, by, in, to …​

### Sentence Capitalization

When capitalizing sentences, capitalize the first letter of the first
word, and any other words normally capitalized in sentences such as
proper nouns.

## Capitalization Table

The following table indicates the capitalization style to use for each
type of user interface element.

| Element                                          | Style    |
|--------------------------------------------------|----------|
| Check box labels                                 | Sentence |
| Command button labels                            | Header   |
| Column heading labels                            | Header   |
| Desktop background object labels                 | Header   |
| Dialog messages                                  | Sentence |
| Drop-down combination box labels                 | Sentence |
| Drop-down list box labels                        | Sentence |
| Field labels                                     | Sentence |
| Text on web pages                                | Sentence |
| Group box and window frame labels                | Header   |
| Items in drop-down and list controls             | Sentence |
| List box labels                                  | Sentence |
| Menu items                                       | Header   |
| Menu items in applications                       | Header   |
| Menu titles in applications                      | Header   |
| Property names displayed in the Properties Panel | Header   |
| Radio button labels                              | Sentence |
| Slider labels                                    | Sentence |
| Spin box labels                                  | Sentence |
| Tabbed section titles                            | Header   |
| Text box labels                                  | Sentence |
| Titlebar labels                                  | Header   |
| Toolbar button labels                            | Header   |
| Tooltips                                         | Sentence |
| Webpage titles and navigational elements         | Header   |

## Quoting

Often text strings will be quoted for display which may be used in
controls that render HTML. Using angle brackets will cause grief for
HTML rendering controls so text should be quoted with single quotes ''.
e.g.:

- 'filename.kicad_pcb'

- 'longpath/subdir'

- 'FOOTPRINTNAME'

- 'anything else'

## Abbreviations

All user interface strings should be spelled out for translation
purposes. The only exceptions are units such as mm, in, etc. or
universally accepted abbreviations across languages such as PCB.

# Dialogs

This section defines how dialog boxes should be designed. The KiCad
project uses the [GNOME User Interface
Guidelines](https://developer.gnome.org/hig/stable/) for laying out
dialogs. When designing dialogs, follow the [visual layout section of
the GNOME User Interface
Guidelines](https://developer.gnome.org/hig/stable/visual-layout.html.en).
KiCad’s dialogs may either be designed with
[wxformbuilder](https://github.com/wxFormBuilder/wxFormBuilder) or
created by hand. Existing dialogs must be maintained in the same way as
they have been implemented. Please use the latest [wxformbuilder
release](https://github.com/wxFormBuilder/wxFormBuilder/releases) to
avoid version mismatch between developers.

## Escape Key Termination

Please note that the escape key termination only works properly if there
is a dialog button defined with an ID of wxID_CANCEL or setting the
escape button ID using [wxDialog::SetEscapeID( MY_ESCAPE_BUTTON_ID
)](http://docs.wxwidgets.org/3.0/classwx_dialog.html#a585869988e308f549128a6a065f387c6)
is called during dialog initialization. The former is the preferred
method for handling escape key dialog termination. There is a checkbox
in wxFormBuilder for setting the "default" control, and this is the one
fired when the "enter" key is pressed.

## Dialog Layout with Sizers

Use wxWidgets "sizers" in all dialogs, no matter how simple they are.
Using absolute sizing in dialogs is forbidden in KiCad. See the
[wxWidgets sizer
overview](http://docs.wxwidgets.org/3.0/overview_sizer.html) for more
information on using sizers. Configure the sizers so that as the dialog
window is expanded, the most sensible use of the increased dialog window
occurs automatically by the sizers. For example, in the DRC dialog of
Pcbnew, sizers should be used to expand the text control to use the full
available free window area, so that the user’s view of the items in the
text control is maximized as he/she expands the dialog window, making it
easier to read more DRC error messages. In other dialogs without one
component more important than the others, the sizers might be configured
to position the controls to sensible positions near the perimeter of the
increasingly larger dialog box, not necessarily leaving them all bundled
tightly together. The dialog box should look nice at any size large
enough to show all the user interface elements.

Avoid defining initial dialog sizes if possible. Let the sizers do their
job. After the dialog is fit to the sizers, set the minimum size to the
current size to prevent the dialog controls from being obscured when
resizing the dialog. If the labels or text of the dialog controls are,
set or changed at run time. Rerun wxWindow::Fit() to allow the dialog to
re-size and adjust for the new control widths. This can all be done
after the dialog is created but before it is shown or use class methods
to re-size the dialog as required. Reset the minimum size to the updated
dialog size.

Dialog windows should not exceed 1024 x 768 when displayed in a 13 point
font. Note that the font used by end users is not something that you
control from within the dialog, but for testing purposes please do not
exceed this dialog size should the user have selected a font size of 13
points. If your dialog exceeds this limit, please redesign the dialog
using tabs or some other paging method to reduce the size of the dialog.

## Dialog Base Class

The KiCad project has a base class which most if not all dialogs should
be derived from. When using wxFormBuilder, please add the following
settings to the "Dialog" tab:

- subclass.name ← DIALOG_SHIM

- subclass.header ← dialog_shim.h

This will provide for an override of the Show( bool ) wxWindow()
function and provide retentive size and position for the session. For
more information, see the [DIALOG_SHIM class source
code](https://gitlab.com/kicad/code/kicad/-/blob/master/common/dialog_shim.cpp).

Use tooltips to explain the functionality of each non-obvious control.
This is important because the help files and the wiki often lag behind
the source code.

## Transferring Data To and From Controls

Dialog data must be transferred to the dialog controls on dialog
initialization and transferred from controls when the dialog is
dismissed by the default affirmative action (typically clicking the
wxID_OK button) or the clicking the wxID_APPLY button. The wxWidgets
dialog framework has support for this by using validators. Please read
the [wxValidator
Overview](https://docs.wxwidgets.org/3.0/overview_validator.html) in the
[wxWidgets documentation](http://docs.wxwidgets.org/3.0/). In the past,
data transfer was handled in various default button handlers virtually
all of which were broken. Do not implement default button handlers in
your dialog code. Use validators to transfer data to and from controls
and allow the default dialog button handlers work the way they were
designed.

## Internationalization

To generate a list of strings occurring in a dialog, one needs to enable
'internationalize' checkbox in the project properties when using
wxFormbuilder. Otherwise, it will not be possible to translate the
dialog.
