title: Footprint Library File Format weight: 42 ---

<div class="formalpara-title">

**Summary The s-expression footprint library file format.**

</div>

\<!--more-→

# Introduction

This documents the s-expression footprint library file format for all
versions of KiCad from 6.0.

- Footprint library files use the `.kicad_mod` extension.

- Footprint library files can only define a single footprint.

- Footprint libraries are defined a folder containing one or more
  footprint library files.

<div class="note">

This file format was introduced with the launch of KiCad 4.0.

</div>

<div class="note">

Prior to version 6 of KiCad, strings were only quoted when necessary.
Saving an older board file to the latest file format will result in
these strings being quoted even though there is no functional change in
the board itself.

</div>

# Layout

A footprint library file includes the following sections:

- [Header](#_header_section)

- [Footprint Definition](#_footprint_section)

# Header Section

The `footprint` token indicates that it is KiCad footprint library file.
This section is required.

<div class="note">

Third party scripts should not use `pcbnew` as the generator identifier.
Please use some other identifier so that bugs introduced by third party
generators are not confused with a footprint library file created by
KiCad.

</div>

    (footprint "NAME"                                               
      (version VERSION)                                             
      (generator GENERATOR)                                         

      ;; contents of the footprint library file...                  
    )

- The footprint `NAME` is a quoted string that defines the name of the
  footprint.

- The `version` token attribute defines the board version using the
  YYYYMMDD date format.

- The `generator` token attribute defines the program used to write the
  file.

- The footprint definition goes here.

# Footprint Section

See the [footprint](../sexpr-intro/index.xml#_footprint) in the
s-expression board common definitions.
