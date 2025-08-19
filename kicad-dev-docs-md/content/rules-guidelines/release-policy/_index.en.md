title: Stable Release Policy weight: 4 summary: The stable release
policy provides guidelines for stable releases provided by the KiCad
project. ---

# 1 Introduction

The purpose of this document is to provide the guidelines for creating
releases for the KiCad project.

<div class="note">

This document is applicable to all stable releases after version 6.0.0.

</div>

<div class="warning">

Do not modify this document without the consent of the project leader.
All changes to this document require approval.

</div>

## 1.1 Versioning Scheme

KiCad uses a typical \[major\].\[minor\].\[bug fix\] versioning scheme.
Each new version is a simple incremental integer of the previous
version. New major releases will reset the minor and bug fix version
back to 0.

## 1.2 Restrictions

No board, schematic, footprint library, symbol library, or project file
format changes are allowed during a major release cycle except to fix
known bugs. Changes to configuration files are acceptable.

Minor and bug fix releases are only valid for the current stable major
release. Once a new stable major version is released, the previous
stable version is considered a no longer supported (dead) branch.

# 2 Major Release Policy

Major releases will occur annually by January 31st of a given calendar
year. This corresponds with the annual [FOSDEM](https://fosdem.org/)
conference. The following table outlines the release schedule dates.

<table>
<caption>Major Release Schedule</caption>
<colgroup>
<col style="width: 16%" />
<col style="width: 83%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">Date</th>
<th style="text-align: left;">Milestone</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p>February 1st</p></td>
<td style="text-align: left;"><p>New feature merge window
opens.</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>September 30th</p></td>
<td style="text-align: left;"><p>New feature merge window closes.</p>
<p>Bug fixing only period begins.</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>December 1st</p></td>
<td style="text-align: left;"><p>String freeze begins.</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>December 15th</p></td>
<td style="text-align: left;"><p>Release candidate 1 tagged.</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>January 14th</p></td>
<td style="text-align: left;"><p>Library, documentation, and,
translation repository freeze.</p>
<p>Bug fix freeze.</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p>January 15th</p></td>
<td style="text-align: left;"><p>Source code repository tagged with next
major version and branched.</p>
<p>Begin package build and upload to website.</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p>January 31st</p></td>
<td style="text-align: left;"><p>New version release
announcement.</p></td>
</tr>
</tbody>
</table>

Major Release Schedule

<div class="warning">

Features not ready by the merge window close will have to wait until the
next merge window. **No exceptions.**

</div>

<div class="note">

Only the source code repository branch is mandatory. All other
repositories are branched if the development team determines it is
necessary.

</div>

# 3 Minor Release Policy

Features from the current development branch of KiCad can be back ported
to the current stable branch provided the following criteria is met:

- The the last feature commit was pushed to the development branch at
  least 60 days prior to merging into the current stable branch.

- The the last known issue filed against the feature occurred at least
  30 days prior to merging into the current stable branch.

- The cherry pick to current stable can be performed without pulling any
  other dependent code that does meets the file format change criteria
  above.

A new minor release will include all of the bug fixes included in the
current bug fix release so the bug fix version will be reset to 0 for
new minor version releases.

<div class="note">

Back ports from the development branch can only be applied to the
current stable branch. Back porting to older stable versions is not
allowed.

</div>

# 4 Bug Fix Release Policy

Bug fix releases are at the discretion of the lead development team.

Bug fix releases will be announced 14 days in advance of the release by
the project manager to allow time for all required release actions to be
completed.

A release candidate `-rc1` tag will be created a minimum of three days
before the final release. The release candidate will be announced by the
project leader on the [KiCad blog](https://www.kicad.org/blog/) along
with the usual KiCad social media sites.

During the release candidate period, only regression fixes since the
last bug fix release are allowed. All other bug fixes must wait until
after the bug fix release period and release announcement are completed.

If a regression is fixed in the release candidate before the three day
minimum testing period ends, a new incremental release candidate will be
created and the three day minimum testing period begins from the date of
the new release candidate. This process repeats until no new regressions
are found in a release candidate. Multiple regressions may be fixed
between release candidates.

The final bug fix release will be tagged if no new regressions occur
within three days of the last release candidate.

<div class="note">

Bug fixes cannot contain translatable string changes except for spelling
and grammar errors. New strings are allowed assuming they do not clash
with any existing strings.

</div>

<div class="note">

Bug fixes can only applied to the current stable release.

</div>
