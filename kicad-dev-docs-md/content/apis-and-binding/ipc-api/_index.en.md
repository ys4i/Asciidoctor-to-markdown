date: 2024-02-06T10:30:00+10:00 title: KiCad IPC API weight: 16 ---

# KiCad IPC API

The IPC API is an interface that can be used to remotely control a
running instance of KiCad. This can be used to implement KiCad plug-ins
as well as to interface KiCad with external software. The API is
currently under development, with the plan of stabilizing the first
version for KiCad 9.0.

## Overall Design Principles

The KiCad API is implemented using Protocol Buffers to define the
structure and contents of messages, and NNG to transport messages
between processes using UNIX sockets. This implementation supports all
supported KiCad platforms, including Windows, macOS, and many Linux/BSD
variants. Each running KiCad instance acts as a server when the API is
enabled, and plugins and other applications act as clients which open
connections to KiCad.

KiCad is an event-driven GUI application, and like most other such
applications, it operates a single thread that handles all events
(background worker threads are used for certain computations to improve
performance). While the API server itself is decoupled from the rest of
KiCad, API events are handled like any other form of user input, and
thus are synchronous. This is an important difference from other APIs
that users may be used to (for example, APIs for web services) which are
often asynchronous to some extent. In practice, this means a few things
must be kept in mind:

1.  The KiCad user may be doing something with the application that
    blocks the API, in order to prevent out-of-order event issues and to
    prevent third-party software from disrupting the user. This means
    that API clients need to anticipate that API messages may sometimes
    timeout or fail because KiCad is busy, and should have a facility to
    retry requests.

2.  An application should not open multiple connections to KiCad at the
    same time. While the underlying transport layer supports this, KiCad
    itself will force an ordering of handling API requests and so there
    is no advantage to doing so.

The API surface described by Protocol Buffers messages ("protobufs") is
stable following the [best
practices](https://protobuf.dev/programming-guides/dos-donts/) described
by the Protocol Buffers team. In general, this means that new versions
of KiCad may introduce new messages and fields, but will not modify the
meaning of existing messages and fields. Items in the API may be
deprecated over time; deprecated messages and fields will be supported
for at least one major version of KiCad after the deprecation is
announced.

Plugins developed for the IPC API are launched in standalone processes
that then communicate with the KiCad process that launched them.
Environment variables are used to inform the plugin about the IPC socket
path and a unique identifier for the running KiCad instance. This allows
multiple instances of KiCad to run side-by-side and launch plugins that
can communicate with the correct instance. Third-party software that is
not launched as a KiCad plugin can also communicate with any running
KiCad instance, however it is up to the third-party software to decide
how (and if) to handle the case of multiple running KiCad instances.

## Detailed information

The [For KiCad Developers](./for-kicad-developers/) section contains
information on the technical implementation of the API on the C++ side,
and guidelines for modifying and extending the API. Please read this
section before working on contributions that touch the API.

The [For Add-on Developers](./for-addon-developers/) section contain
information for users of the API. Please read this section if you are
interested in developing KiCad plugins or interfaces with third-party
software.
