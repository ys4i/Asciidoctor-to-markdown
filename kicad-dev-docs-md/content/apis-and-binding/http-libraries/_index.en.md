date: 2024-04-08T14:30:00+10:00 title: HTTP Libraries weight: 18 ---

# HTTP Libraries

HTTP libraries are a type of KiCad symbol library that sources data
about parts for an external source such an ERP system. They do not
contain any symbol or footprint definitions as standard KiCad libraries
do. Instead, they **reference** to symbols and footprints found in other
KiCad libraries.

HTTP libraries are read only for now but will eventually support
read/write in the future. At the moment only REST or REST-Like APIs are
supported but support for other libraries could be added easily.

## Authentication

Authentication is done via an **Access Token** only; usually issued by
the providing application. The token will be used in every request using
the following header: `'AUTHORIZATION': 'Token usertokendatastring'.`

## REST API Endpoint Definition

Different to ODBC, the REST API provides the information already
pre-formatted. KiCad does not know anything about tables, nor does it
need to know anything about the underlying database structure of the
providing application (API).

KiCad expects a root-URL and an api-version to access the library. This
must be provided via the **Configuration File**. There are two endpoints
which will provide all data necessary to KiCad. Those are called:

- **categories**

- **parts**

# Examples

Return all parts belonging to a category with **ID = 16** using the
**root-url** and the **api-version** as shown in the config file:
**{root-url}/{api-version}/parts/category/16.json**. In relation to the
example config file this means:
**http://localhost:8000/kicad-api/v1/parts/category/16.json**.

Another example would be using a **non integer ID** such as
**Resistors**
**{root-url}/{api-version}/parts/category/Resistors.json**.

As shown further below, KiCad expects an ID (numeric or not) which is
used to query the API and an optional human-readable name which is
displayed as part name to the user. If the API does not return the
optional `name` key, KiCad will use the `id` key instead as part name.

**Please note that KiCad expects strings only. All integers, Boolean
values, doubles, floats etc must be converted to strings when responding
to requests.**

## Endpoint Validation

KiCad queries the root-URL on **{root-url}/{api-version}/** and expects
it to return a dictionary with the endpoints as key-value pairs.

    HTTP_200_OK
    {
        "categories": "",
        "parts": ""
    }

**Note: Only keys are validated. The values can be left blank or set to
the actual URL.**

## Fetching Categories

To fetch the categories, KiCad queries
**{root-url}/{api-version}/categories.json** using a standard GET
request. KiCad expects the server to respond with a JSON formatted array
holding one or more categories. It expects only an **id** and a **name**
for each category and will display those as libraries inside the
**Symbol Chooser**. Part belonging to this category will be displayed
underneath those respectively.

    HTTP_200_OK
    [
        {
            "id": "16",
            "name": "Active Parts/Clock and Timer ICs",
            "description" : "A description of Active Parts/Clock and Timer ICs"
        },
        {
            "id": "17",
            "name": "Active Parts/Driver ICs",
            "description" : "A description of Active Parts/Driver ICs"
        },
        {
            "id": "20",
            "name": "Active Parts/Embedded Processors and Controllers",
            "description" : "A description of Active Parts/Embedded Processors and Controllers"
        },
        {
            "id": "22",
            "name": "Active Parts/Interfaces",
            "description" : "A description of Active Parts/Interfaces"
        },
        {
            "id": "15",
            "name": "Active Parts/Logic ICs",
            "description" : "A description of Active Parts/Logic ICs"
        }
    ]

## Fetching Parts for Symbol Chooser

To display parts underneath a certain category inside KiCad’s **Symbol
Chooser**, KiCad queries the parts endpoint using a standard GET
request: **{root-url}/{api-version}/parts/category/16.json**; with 16
being a numerical example ID for a specific category. As server
response, KiCad expects an array containing one or more parts for that
specific category.

For each part KiCad expects only an `id`. If there is a need to display
a different name to the `id` inside the **Symbol Chooser**, one can use
the optional `name` key as shown below. Additionally, one can also
provide a part description using the `description` key.

    HTTP_200_OK
    [
        {
            "id": "69",
            "name": "830050789",
            "description": "CRYSTAL 32.7680KHZ 12.5PF SMD"
        },
        {
            "id": "40",
            "name": "NE555PSR",
            "description": "IC OSC SINGLE TIMER 100KHZ 8SO"
        },
        {
            "id": "238",
            "name": "TLC555CPSR",
            "description": "IC OSC SINGLE TIMER 2.1MHZ 8SO"
        }
    ]

**TIP**: Since the part details are just for the high-level overview
within the **Symbol Chooser**, one should only respond with the minimum
data needed. This will speed up the data acquiring process and
consequently enhance the user experience. Therefore, KiCad expects only
the minimum amount of detail needed (see example above) to display the
parts inside the **Symbol Chooser**. Any other key/value pairs are
ignored at this stage. However, the API can send the full details if
that’s easier to implement and bandwidth is not a concern.

## Fetching Detailed Part Information

When a user clicks on a part inside the **Symbol Chooser**, KiCad will
try to retrieve full detailed information about the part using the parts
endpoint and a standard GET request:
**{root-url}/{api-version}/parts/16.json**. KiCad expects a single JSON
object with the following keys as shown below (Note: the `name` key is
used in this example).

The dict `fields` can contain any number of additional key/value pairs;
this includes being an empty dictionary! A **key** represents a **FIELD
Name** which is visible in KiCad’s symbol editor. The server can provide
as many fields as needed; there is no limit to it.

Each **KiCad FIELD** is represented using a dict, and must contain at
least the the `value` key. Additionally, the API can return whether or
not the field is displayed using the optional `visible` key. If this key
is not specified, KiCad will display the field by default.

As mentioned above all types **must** be converted to strings. Allowed
booleans are: "1", "0", "true", "false", "yes", "no", "y", "n". The
strings are case-insensitive.

    HTTP_200_OK
    {
        "id": "16",
        "name": "R_0R0_0603_0.125W_1%",
        "symbolIdStr": "Device:R",
        "exclude_from_bom": "False",
        "exclude_from_board": "False",
        "exclude_from_sim": "True",
        "fields": {
            "footprint": {
                "value": "Resistor_SMD:R_0603_1608Metric",
                "visible": "False"
            },
            "datasheet": {
                "value": "www.kicad.org",
                "visible": "False"
            },
            "value": {
                "value": "0R0"
            },
            "reference": {
                "value": "R"
            },
            "description": {
                "value": "I am a resistor",
                "visible": "False"
            },
            "keywords": {
                "value": "RES passive smd",
                "visible": "False"
            },
            "custom1": {
                "value": "MyText1",
                "visible": "False"
            },
            "custom2": {
                "value": "MyText2",
                "visible": "False"
            },
            "custom3": {
                "value": "MyText3",
                "visible": "False"
            }
        }
    }

## Symbol Attributes

The API provides the functionality to include exclusion flags, as
illustrated in the example above. These attributes serve to specify
certain preferences within the KiCad software. The following exclusion
flags are currently supported:

`exclude_from_bom`

`exclude_from_board`

`exclude_from_sim`

It’s important to note that if one ore more of these exclusion flags are
not explicitly specified, KiCad will assume that they are not set for
exclusion. In other words, the default behavior is to include all items
and features in the relevant processes (BOM generation, board layout,
and simulation) unless otherwise specified using these exclusion flags.

## Server Response Codes

If KiCad receives anything else than HTTP 200, it will simply display an
error message to the user and ignore that specific request result
entirely. This means that KiCad could end up not displaying some or any
categories or parts at all if the API does not comply.
