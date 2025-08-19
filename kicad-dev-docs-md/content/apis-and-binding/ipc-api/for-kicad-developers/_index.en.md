date: 2024-02-06T10:30:00+10:00 title: For KiCad Developers weight: 16
---

# API Documentation for KiCad developers

The IPC API is implemented inside KiCad by a server thread that listens
on a UNIX socket for connections using the nanomsg next generation (nng)
protocol. At the moment, this socket operates in request-reply mode,
with KiCad as the server, so asynchronous notifications to API clients
is not possible. This server passes messages to the rest of KiCad using
a wxWidgets event to cross onto the main (UI) thread. As with all other
interactive KiCad events, the rest of API processing happens on the UI
thread.

Messages are contained within an envelope structure, which is unpacked
and then dispatched to a request handler according to the type of the
interior Protobuf message. Request handlers are functions that typically
belong to a subclass of the `API_HANDLER` class. The low-level
infrastructure of the API all lives in `kicommon` and is shared between
all parts of KiCad, but request handlers are dynamically registered and
so can be attached to specific frames. If a certain message does not
have a handler registered to it, the API server will return an error to
the client.

Handlers are processed one by one, and it is possible to register more
than one handler for a given message type. This is useful for situations
where a message has some specifying target information that can narrow
down its effect to a subset of available handlers. For example, the
`GetOpenDocuments` message takes a `DocumentType` parameter that
specifies which type of documents to query. Each open editor frame can
register a handler for the same message, and return
`ApiStatusCode::AS_UNHANDLED` if they don’t handle that type of
document. In this way, the dispatcher can try to have a message handled
by multiple handlers, and only return an error to the client if none of
the available handlers return success.

A block diagram showing a typical request from a Python plugin is shown
below. The API is language-agnostic; `kicad-python` is used here as one
example of a language binding.

    ┌─────────────────┐
    │ KiCad Internals │
    └───────▲─────────┘
            │
    ┌───────┴─────────┐
    │ REQUEST_HANDLER │
    └───────▲─────────┘
            │
     ┌──────┴────────┐
     │ API_HANDLER_* │
     └──────▲────────┘
            │
      ┌─────┴──────┐                    ┌─────────────┐
      │ API_SERVER │                    │ Plugin Code │
      └─────▲──────┘                    └─────┬───────┘
            │                                 │
        ┌───┴───┐                      ┌──────▼───────┐
        │ KINNG │                      │ kicad-python │
        └───▲───┘                      └──────┬───────┘
            │                                 │
         ┌──┴──┐                          ┌───▼───┐
         │ nng │                          │ pynng │
         └──▲──┘                          └───┬───┘
            │                                 │
     ┌──────┴──────┐                   ┌──────▼──────┐
     │ UNIX socket ◄───────────────────┤ UNIX socket │
     └─────────────┘                   └─────────────┘

## Transport and wire protocol

The underlying transport mechanism for the API is
[nng](https://github.com/nanomsg/nng), which is a library providing
messaging primatives, including inter-process communication (IPC). The
IPC mechanism used by KiCad’s API is to serialize protocol buffers to
bytes and send them over a UNIX socket. KiCad does not currently make
use of any of the more advanced features of nng.

The socket is opened at `/tmp/kicad/api.sock` on macOS and Linux, and in
a subdirectory of the user’s temporary file directory on Windows. If
multiple instances of KiCad are launched, the socket filename for every
KiCad instance other than the first will be `api-<PID>.sock`, where
`<PID>` is the process identifier of the KiCad instance.

## Protocol Buffers

The API itself is implemented as [Protocol
Buffers](https://protobuf.dev/) (protobufs), a way of describing and
serializing messages that, if used correctly, can provide stability for
API clients while allowing KiCad internals to evolve. It is important
for every KiCad developer who works on the API to read and understand
the protobuf [programming best
practices](https://protobuf.dev/programming-guides/dos-donts/) and [API
best practices](https://protobuf.dev/programming-guides/api/), as the
KiCad API attempts to follow these guidelines in most circumstances.
Some of the most important practices are:

1.  Care must be taken whenever evolving the API in a way that seeks to
    deprecate, rename, or otherwise change the existing messages and
    fields. In general, the API should be designed as much as possible
    to support both the current state of KiCad and anything we know
    about our desired future state, to avoid needing to deprecate lots
    of messages in the future.

2.  Plain data types should used sparingly. It is almost always a better
    idea in terms of future- proofing and API evolution to wrap data
    types in a container message, even if these types are represented by
    a single plain old data variable in KiCad’s C++ internals. This is
    particularly true for boolean values: they should almost never be
    included in protobuf messages. Instead, an enum type should be
    defined that distinguishes between UNKNOWN (which should always have
    the value of `0`) and `false`. This also makes it possible to swap
    out the boolean for a tri-state at a later point in time with no
    compatibility issues.

3.  Be careful about message nesting. It is not always a good idea to
    attempt to duplicate the object hierarchy of KiCad’s internal C
    classes into a hierarchy of messages. Sometimes, KiCad's object
    hierarchy comes more from developer convenience or history than
    logical design, and this hierarchy doesn't always make sense at an
    abstract conceptual level. The API should, in general, be
    \*conceptually\* logical and not have its design influenced by how
    KiCad's C is designed.

## Enumerations

Exposing enumerations to the API requires special care to minimize the
chance of bugs and compatibility issues. To expose a new C++ enum type
to the API, do the following:

1.  Define a new protobuf enum for the type. This enum should have a
    CamelCase name that clearly describes the type — use this as a
    chance to double-check if the KiCad type is named clearly.

2.  Fill out the values for the protobuf enum. Each value should be an
    UPPER_SNAKE_CASE name, and begin with a prefix to avoid namespace
    conflicts (protobuf enums are implemented as plain enums, not C enum
    classes). That prefix is conventionally the abbreviation of the
    CamelCase enum name, for example \`StrokeLineStyle\` has values like
    \`SLS_SOLID\`, but this can be deviated from where it makes sense.
    The first enum value must always be \`\<prefix\>\_UNKNOWN\` and must
    be assigned the value \`0\`. This value represents a message where
    this enum has not been set, and does not map to an equivalent C enum
    value. Because of this, and because protobuf enums may not contain
    negative numbers, it is generally not going to be the case that
    there is a 1:1 mapping between C++ enum value and protobuf enum
    value.

3.  Define a specialization of `FromProtoEnum` and `ToProtoEnum` for
    your new enum. There are several source files where these
    specializations could go, based on what compilation unit your enum’s
    C++ definition lives in. For example, shared (common) enums go in
    `common/api/api_enums.cpp`.

4.  Add your enum to the QA tests in `qa/tests/api/test_api_enums.cpp`.
    Check that the tests pass, which will confirm that your To/From
    function implementations are correct.

Note that as enum definitions evolve over time in KiCad, the protobuf
versions must never have values deleted or modified: they can only have
values deprecated and new values added! Use the
`ToProtoEnum`/`FromProtoEnum` functions to handle any changes in how the
values should map to KiCad internals.

## Adding, Modifying, and Removing API surface

Please follow these guidelines when making any changes or additions to
the API or to code that depends on the API surface (for example, when
adding, removing, or modifying the behavior of an object’s properties):

1.  Never rename, re-order, or change the functional meaning of existing
    fields in any Protobuf messages. The only changes permitted are to
    add new fields or to remove deprecated fields by commenting them out
    (preserving their original field index, which must never be
    re-used).

2.  Never remove fields in a bugfix release of KiCad. If a field is
    deprecated, add a comment above it noting the deprecation (and any
    replacement, if applicable). The field must remain in the message
    until the next major KiCad release (at minimum). If it is necessary
    to deprecate a field in a bugfix release, the serialization code
    must continue supporting the deprecated field as best as possible
    (for example, serializing a default value even if the field no
    longer exists on the source object, and accepting but ignoring the
    value when deserializing).

3.  Never remove or change the meaning of any Protobuf enum values. If
    enums on the KiCad side change such that a Protobuf enum value no
    longer makes sense, mark it as deprecated with an appropriate
    comment, and update the `ToProtoEnum` and `FromProtoEnum` functions
    to handle the deprecated value in whatever way makes sense.

4.  Please document fields that are added to existing messages with a
    note such as `// Since: 9.0.1` in the comment line above the field.
    These comments will be included in client bindings, so adding
    information about when new APIs were introduced will help make sure
    the binding documentation is kept up-to-date.

## Object Serialization

Objects on the drawing canvas (in other words, the KiCad classes that
represent "real things" that the user places, modifies, etc) are all
subclasses of the `SERIALIZABLE` interface. This means that in order to
expose them to the API, all you need to do is:

1.  Define a protobuf message describing that object

2.  Add the object to the QA tests in
    `qa/test/api/test_api_proto.cpp` — you may need to extend the
    `api_kitchen_sink.kicad_pcb` test file with instances of your
    object. Make sure there is at least one instance of your object that
    has all its properties set to non-default values for best test
    coverage.

3.  Implement the `::Serialize` and `::Deserialize` methods, and iterate
    on them until the QA tests pass.

When adding new object messages, look at what exists already and try to
be consistent. Design for the desired conceptual representation of an
object, even if that doesn’t completely match the current C++
implementation. And only include "concrete" data about the objects in
the message. Calculated/computed properties of an object should rarely
be included in an object’s main message.

Be careful about datatypes as well: as described above, plain data types
(ints, bools, strings) are usually not the right choice to describe a
property, as they can limit the ability to evolve that property over
time. Prefer enums and container messages.

Use protobuf message hierarchies sparingly — it is generally not a good
idea to encode KiCad’s C++ class hierarchies into messages as described
above. For example, in KiCad, a `PCB_TRACK` and a `PCB_VIA` are related
by inheritance, but there are separate `Track` and `Arc` protobuf
messages that duplicate their shared properties, rather than defining
the `Arc` message as containing an inner `Track` message. The cost of a
slight increase in code duplication is worth it because it prevents
confusing/convoluted API surfaces (protobuf does not support
inheritance, so the arc properties would need to be accessed as
`arc.track.start` and `arc.mid` for example) and reduces interdependence
between messages that could make API evolution more painful in the
future.

## Common data types

The `base_types.proto` file contains a number of data type definitions
that are used throughout the API. Use these (and expand them) instead of
defining more specific types, where it makes sense. Some of the types to
be aware of: `KIID`, `Vector2`, `Distance`, `Angle`, `Ratio`, `Color`,
etc.

Note that all physical distances that represent concrete object
properties in the API are represented as 64-bit integer nanometer
values, regardless of what part of KiCad they come from.

## API Handlers

A handler function is any function-like construct with the following
signature:

``` cpp
HANDLER_RESULT<ResponseType>( HandlerType::* aHandler )( RequestType&, const HANDLER_CONTEXT& )
```

In this signature, `RequestType` and `ResponseType` are protobuf message
classes, and `HANDLER_RESULT<T>` is a typedef for
`tl::expected<T, ApiResponseStatus>`. `tl::expected` is a library that
backports `std::expected` to older C++ and adds some functionality
beyond the STL spec. This type represents either an expected value, or
an unexpected value that contains more information about why something
failed. The expected value is always just the response message that we
want to send back to the client, and the unexpected value,
`ApiResponseStatus`, is a message that contains a status code as well as
an error message string.

For example:

``` cpp
e.set_status( ApiStatusCode::AS_BAD_REQUEST );
e.set_error_message( fmt::format( "the client {} already has a commit in progress",
                                  aCtx.ClientName ) );
return tl::unexpected( e );
```

Notice that the error message begins lowercase and has no punctuation.
This is because the error returned by this handler will almost always be
composed with other fragments to form a full sentence (for example, the
text `"KiCad API error: "` might be prepended).

The status code `ApiStatusCode::AS_UNHANDLED` is used as a special flag
to note that this handler can’t handle the given request, but there was
nothing in particular wrong with the request. This is used in situations
when the same request is routed to multiple handlers, one after the
other, in case one of them is able to handle the request.

The base class `API_HANDLER_EDITOR` is used to provide many of the
common handlers that all the graphical editors in KiCad need, such as
object CRUD, document retrieval, etc. This is then specialized into
`API_HANDLER_PCB`, `API_HANDLER_SCH`, and so on. Handlers that don’t
require an editor frame to be open are in `API_HANDLER_COMMON`.

Handlers always get passed a `HANDLER_CONTEXT` structure that contains
information about the API request that is in flight. This can be used to
tell apart multiple different API clients, for example.
