title: Code Design Guidelines weight: 5 summary: The code design
guidelines are rules that must be met for any contribution to KiCad’s
application code base. ---

# 1. Introduction

The purpose of this document is to provide a guide for KiCad developers
about source code dos and don’ts in KiCad. It is not a comprehensive
guide because it does not cover all topics. However, it is expected that
all of the topics covered by this document are followed by all
developers contributing code to the KiCad project.

# 2. Library Dependencies

KiCad is a cross platform project. Therefore, all dependency libraries
must be packaged for all [supported
platforms](https://www.kicad.org/help/system-requirements/). If packages
are not available on a given platform, it is the responsibility of the
developer introducing any new dependencies to ensure that they are
packaged before submitting the new dependency. Platform specific
features will not be accepted.

# 3. Low Level Object Design

All KiCad low level objects not directly related to user interface
design must be free of user interface code dependencies. This includes
all `SCH_*`, `LIB_*`, `PCB_*`, `FP_*`, and I/O plugin objects.

<div class="note">

The only exception is the [wxWidgets Debugging
Macros](https://docs.wxwidgets.org/3.0/group__group__funcmacro__debug.html).
The reason for this is that the wxWidgets user interface code is only
used in debug builds. This has the advantage of developers being made
aware of coding issues without having to run from the command line or in
a debugger to see assertion messages.

</div>
