title: Commit Message Format Policy weight: 3 summary: The guidelines
describing how git commit messages should be formatted. ---

# Commit Message Format Policy

Commit messages must begin with a brief subject line. The subject line
limit is 72 characters. If there is a body message, it must be separated
from the subject line by a blank line and wrapped at 75 characters.

The body of a commit message should explain what the commit does and
why. Do not explain **how** the changes work as the code itself should
do that.

# Linking a Commit to an Issue

If your commit fixes an issue that has been reported in the [issue
tracker](https://gitlab.com/kicad/code/kicad/issues), add a blank line
after the subject line or message body and add a line indicating the
fixed issue number to your commit message. In such cases, GitLab will
automatically close the issue and add a link to the commit in the issue.

For example, the following line will automatically close issue
\#1234567:

    Fixes https://gitlab.com/kicad/code/kicad/issues/1234567

There is an [alias](#commit_fixes_alias) to simplify this step. Read
more about automatic issue closing in the [GitLab
documentation](https://docs.gitlab.com/ee/user/project/issues/managing_issues.html#closing-issues-automatically).

# Changelog Tags

To facilitate following the code changes, include a changelog tag to
indicate modifications noticeable by the users. There are three types of
changelog tags:

- `ADDED` to denote a new feature.

- `CHANGED` to indicate a modification of an existing feature.

- `REMOVED` to inform about removal of an existing feature.

There is no need to add changelog tags for commits that do not modify
the way the users interact with the software, such as code refactoring
or a bug fix for unexpected behavior. The purpose of the changelog tags
is to generate release notes and notify the documentation maintainers
about changes.

## Making the Documentation Developers Aware of Changes

When a commit with changelog tag is pushed, the committer should create
a new issue in the [documentation
repository](https://gitlab.com/kicad/services/kicad-doc/issues) to
notify the documentation maintainers. Include a link to the commit
containing the reported changes.

## Extracting Changelogs

Thanks to the changelog tags, it is easy to extract the changelog using
git commands:

``` sh
    git log -E --grep="ADD[ED]?:|REMOVE[D]?:|CHANGE[D]?:" --since="1 Jan 2017"
    git log -E --grep="ADD[ED]?:|REMOVE[D]?:|CHANGE[D]?:" <commit hash>
```

KiCad provides an [alias](#commit_changelog_alias) to shorten the
changelog extraction commands.

## Example

Following is an example of a properly formatted commit message:

        Eeschema: Adding line styling options

        ADDED: Add support in Eeschema for changing the default line style,
        width and color on a case-by-case basis.

        CHANGED: "Wire" lines now optionally include data on the line style,
        width and color if they differ from the default.

        Fixes https://gitlab.com/kicad/code/kicad/issues/594059
        Fixes https://gitlab.com/kicad/code/kicad/issues/1405026

# Git Aliases File

There is a file containing helpful git aliases located at
`tools/git/fixes_alias`. To install it, run in the source repository:

``` sh
    git config --add include.path $(pwd)/tools/git/fixes_alias
```

## 'fixes' Alias

Once the alias configuration file is installed, it may be used to amend
the most recent commit to include the bug report link:

    git fixes 1234567

For example, the command below will append a line to the last commit
message:

    Fixes https://gitlab.com/kicad/code/kicad/issues/1234567

## 'changelog' Alias

With the alias configuration file installed, changelogs can be extracted
by running the following:

``` sh
    git changelog --since="1 Jan 2017"
    git changelog <commit hash>
```
