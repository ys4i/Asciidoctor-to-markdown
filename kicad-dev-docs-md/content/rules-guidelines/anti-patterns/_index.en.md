title: Anti-Patterns weight: 11 summary: Anti-pattern guidelines for
writing code for the KiCad project. ---

# Introduction

The purpose of this document is to provide the guidelines for how
**not** to write code for the KiCad project. Anti-patterns are code
practices that seem to occur often but shouldn’t. If your code contains
any of these patterns, please fix them.

<div class="warning">

Do not modify this document without the consent of the project leader.
All changes to this document require approval.

</div>

## Bare Enumerations

Bare enums are declared like this

``` c++
enum LAST_LAYOUT {
    NONE,
    ALIAS,
    PARENT
};
```

These are problematic because they declare the simple keywords `NONE`,
`ALIAS` and `PARENT` into the global scope. These then clash with enums
or worse, defines, that exist in other header or source files used in
the project.

The solution is to use [scoped
enums](https://en.cppreference.com/w/cpp/language/enum#Scoped_enumerations).
In this form, the enum is

``` c++
enum class LAST_LAYOUT {
    NONE,
    ALIAS,
    PARENT
};

LAST_LAYOUT l = LAST_LAYOUT::NONE;

switch(l)
{
    case LAST_LAYOUT::NONE   : std::cout << "NONE\n";   break;
    case LAST_LAYOUT::ALIAS  : std::cout << "ALIAS\n";  break;
    case LAST_LAYOUT::PARENT : std::cout << "PARENT\n"; break;
}
```

## Dereferencing Pointers

Dereferencing an invalid pointer almost always results in a crash.
**Never** assume a pointer is valid. Even if the code in question has
never crashed before, that doesn’t mean that someone wont break
something in the future. Use assertions to help developers catch invalid
pointers during development. Even better than assertions, you can use
the [ wxWidgets debugging
macros](https://docs.wxwidgets.org/3.2/group__group__funcmacro__debug.html)
which can raise assertions on debug builds and gracefully handle run
time issues in release builds. If you are concerned about pulling in
[wxWidgets](https://www.wxwidgets.org/) user interface code, there are
header only C++ assertion libraries available.

Avoid the temptation to silently hide potentially bad pointers. The
following code:

``` c++
void SomeFunction( SomeObject* somePointer )
{
    if( somePointer )
    {
        somePointer->DeReferencedMethod();
    }
}
```

should be replaced with:

``` c++
void SomeFunction( SomeObject* somePointer )
{
    // This will trigger a wxWidgets assertion dialog in debug builds and silently
    // return in release builds which is far better than crashing.
    wxCHECK( somePointer, /* void */ )

    somePointer->DeReferencedMethod();
}
```

## wxUpdateUIEvent Abuse

Avoid using [
`wxUpdateUIEvent`](https://docs.wxwidgets.org/3.2/classwx_update_u_i_event.html)
for anything other than the methods provided by the event object.
Updating controls in a `wxUpdateUIEvent` handler will typically trigger
other control events that in turn will fire more wxUpdateUIEvents which
results in an infinite event loop that can cause the user interface to
become less responsive.

If you are not sure you are abusing `wxUpdateUIEvent`, the advanced
configuration setting `UpdateUIEventInterval` allows you to set the time
in milliseconds which the update events are generated or -1 to disable
all update events. If slowing or disabling the update events makes the
user interface more responsive, a `wxUpdateUIEvent` handler is the
problem.

## Translating Partial Sentences

Never break an translated sentence into multiple `_()` tags. This
prevents translations from understanding your intention and from
creating grammatically correct sentences in languages other than
English.

For example the following is incorrect:

``` c++
ShowReport( _( "Duplicate instances of " ) + m_changeArray[j].NewRefDes );
```

This should be replaced by the following:

``` c++
ShowReport( wxString::Format( _( "Duplicate instances of %s" ), m_changeArray[j].NewRefDes ) );
```

The new formulation allows a translator to move the object of the
sentence. In the original formulation, the object (the `NewRefDes`
string) would always appear at the end of the sentence, which may not be
grammatically correct in non-English languages.

Similarly, do not break sentences into parts like this:

``` c++
wxString verb  = updateMode ? _( "Update" ) : _( "Change" );
wxString warning = _( "%s all items on the board?" );

wxMessageBox( wxString::Format( warning, verb ) );
```

In English, the word `Update` is both a noun and a verb. Without
context, the translator will not know which version to use.
Additionally, it will not be clear to the translator that the `verb`
string is used by the `warning` string as they will not appear together
in the translation file.

Instead, we must write the sentences explicitly:

``` c++
wxString msg = updateMode ? _( "Update all items on the board?" ) :
                            _( "Change all items on the board?" );

wxMessageBox( msg );
```

## Dialogs

### Breaking Close Event Handling

Modal and Quasi-modal dialog windows closure is handled automatically
when the standard button IDs (wxID_OK, wxID_CANCEL, wxID_CLOSE, etc.) as
well as the close button title bar decorator. There is no need to handle
[wxCloseEvent](https://docs.wxwidgets.org/3.2/classwx_close_event.html)
directly unless the dialog has special requirement such as vetoing the
event. Very few dialogs require any special handling of close event. For
more information see the [ wxWidgets closing windows
documentation](https://docs.wxwidgets.org/3.2/overview_windowdeletion.html#overview_windowdeletion_close).
If you do not fully understand the default dialog close event handling,
do not implement one in your dialog. Doing so will most likely break the
default handling.

### Avoid Direct Access of Parent

Avoid passing a specific parent window pointer type to the dialog and
saving it for use dialog methods. At best, this is a bad coding practice
as it limits the re-usability of the dialog to a given parent window
type. At worst, it can cause crashes in [mode-less dialog
windows](#_mode_less_dialogs) when accessing the point in the dialog
object destructor.

If you need to pass information to the parent window, use the [wxWidgets
events](https://docs.wxwidgets.org/3.2/overview_events.html) instead.

This:

``` c++
MY_DIALOG::MY_DIALOG( SCH_EDIT_FRAME* aParent )
{
    m_frame = aParent;
}

void MY_DIALOG::SomeMethod()
{
    m_frame->SomeFrameMethod();
}
```

should be replaced with this:

``` c++
MY_DIALOG::MY_DIALOG( wxWindow* aParent )
{
}

void MY_DIALOG::SomeMethod()
{
    wxWindow* parent = GetParent();

    if( parent )
    {
        SOME_EVENT evt();

        // If the parent doesn't handle this event it goes into the bit bucket.
        parent->HandleWindowEvent( evt );
    }
}
```

### Object Property Modifiers

Modal and Quasi-modal dialog windows should be implemented as simple
object property modifiers. Avoid modifying properties of live objects.
Make a copy of the object being modified to transfer property
information between the dialog controls and the object properties.

``` c++
MY_DIALOG::MY_DIALOG( wxWindow* aParent, const LIB_PIN& aPin )
{
    m_pinCopy( m_Pin );     // Make a copy of the LIB_PIN object to modify.
}
```

### Handling the OK Button

Dismissing a modal or quasi-model dialog window by clicking on the "OK"
button does not indicate that any of the object properties have been
changed. Always compare the properties from the dialog object against
the properties of the original object. Only set the document modified
flag if the object properties have been modified.

``` c++
MY_DIALOG dlg( this, m_somePin );

if( dlg.ShowQuasiModal() == wxID_OK && dlg.GetPin() != m_somePin )
{
    // Update the undo/redo handling here.

    m_somePin = dlg.GetPin();

    // Set modified flag here.
}
```

### Mode Specific Coding

Avoid adding mode specific code to dialogs. The dialog cannot know how
it is going to be shown in advance. Making assumptions about how the
dialog is shown from within the dialog can lead to unexpected bugs.

``` c++
void MY_DIALOG::OnCloseEvent( wxCloseEvent& aEvent )
{
    // The code will clean up the dialog when it is mode-less but it also will
    // completely bypass the automatic data transfer handling for modal and
    // quasi-modal dialogs.
    Destroy();
}
```

### Mode-less Dialogs

#### Memory Management

[wxWidgets](https://www.wxwidgets.org/) mode-less dialogs are always
created on the heap using the C \`new\` operator. Therefore, it is the
developers responsibility to manage the memory allocated for the dialog.
However, https://www.wxwidgets.org/\[wxWidgets\] windows object memory
should never be cleaned up using the C `delete` operator.
[wxWidgets](https://www.wxwidgets.org/) provides the
[Destroy()](https://docs.wxwidgets.org/3.2/classwx_window.html#a6bf0c5be864544d9ce0560087667b7fc)
method to perform any required window tear down and then deletes the
allocated memory.

#### Dialog Life Time

[wxWidgets](https://www.wxwidgets.org/) does **not** guarantee that a
parent window will out live a mode-less dialog created by it. Therefore,
attempting to access the parent window from the dialog’s destructor
[(See Avoid Direct Access of Parent)](#_avoid_direct_access_of_parent)
may crash. If you have to access the parent from the the dialog
destructor, check to make sure the dialog has been shown in either the
modal or quasi-modal mode.

Don’t do this:

``` c++
MY_DIALOG::~MY_DIALOG()
{
    m_parent->SomeParentWindowMethod();   // May crash if the dialog is mode-less.
}
```

instead do this:

``` c++
MY_DIALOG::~MY_DIALOG()
{
    // Ensures the parent is still valid.
    if( IsModal() || IsQuasiModal()
        m_parent->SomeParentWindowMethod();
}
```

## S-Expression File Format Anti-patterns

### Unquoted strings

Always quote strings in KiCad file formats, whether or not they contain
user-generated data or you think they might need to be quoted.

    // Do this
    (generator "eeschema")

    // Not this (assuming that 'eeschema' is not a token)
    (generator eeschema)

### Non-standard boolean flags

Use the tokens `yes` and `no` for booleans in the file format. All
boolean flags should be a two- element list of the form
`(token [yes|no])`. Do not use implicit-true booleans. Boolean flags may
be omitted from formatting where it makes sense to do so (and the parser
should have a defined default value that will be assumed if the flag is
missing), but if the boolean token is present, an explicit value must
also be present.

    // Do this
    (my_item
       (hide yes)
    )

    // Or this
    (my_item
        (show no)
    )

    // Not this (an implicitly-true boolean)
    (my_item
        (hide)
    )

    // And not this (a boolean outside an enclosing list)
    (my_item hide yes)

### Ignoring unneeded or deprecated tokens implicitly

When implementing a parser that needs to skip over tokens in a file that
are not needed, always do so explicitly, by parsing the tokens and doing
nothing with them. Do not do this implicitly by skipping a certain
number of symbols.

``` c++
// Do this
for( token = NextTok(); token != T_RIGHT && token != EOF; token = NextTok() )
{
    if( token == T_LEFT )
        token = NextTok();

    switch( token )
    {
    case T_generator:
        // Skip (generator "generatorname")
        NeedSYMBOL();
        NeedRIGHT();
        break;
    //...
    }
}

// Don't do this
for( token = NextTok(); token != T_RIGHT && token != EOF; token = NextTok() )
{
    if( token == T_LEFT )
        token = NextTok();

    switch( token )
    {
    case T_version:
        m_requiredVersion = parseInt( FromUTF8().mb_str( wxConvUTF8 ) );
        NeedRIGHT();

        // Skip (generator "generatorname")
        NeedLEFT();
        NeedSYMBOL();
        NeedSYMBOL();
        NeedRIGHT();
        break;
    //...
    }
}
```
