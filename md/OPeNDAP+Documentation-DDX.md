The DDX is an attempt to combine the information in the both the DDS and
DAS into one response to eliminate the need to cross-walk the two
responses when applying DAS information to variables. It will also
streamline access somewhat and will employ XML as the content-type,
although these are secondary objectives.

## DAP 3.1

DAP 3.1 contains an implementation of this feature, which is described,
in spirit, in the original [Draft DAP 4 specification
document](Media:Dap_objectsdraft3nov03.pdf "wikilink").

## DAP 3.2

### Implemented

#### GRDDL Namespace

In version 3.2 the namespace for *grddl* is defined and the DDX to RDF
XSL transform is the value of the *grddl:transform* attribute in the
*Dataset* element.

`xmlns:grddl="`[`http://www.w3.org/2003/g/data-view#`](http://www.w3.org/2003/g/data-view#)`"`
`grddl:transformation="`[`http://xml.opendap.org/transforms/ddxToRdfTriples.xsl`](http://xml.opendap.org/transforms/ddxToRdfTriples.xsl)`"`

#### BLOB element

Removed as of Version 3.2.

#### Version information and the structure of the *Dataset* element

The information in the DDX *Dataset* element is different starting with
DAP 3.2. For this version onward, the *Dataset* element will contain a
default namespace that is *<http://xml.opendap.org/ns/DAP/3.2#>* where
the last pathname component will vary to reflect the version number.
This will also be set as a prefix like *dap3.2* so that attributes can
be put in the namespace. The *Dataset* element will also have the schema
location set to *<http://xml.opendap.org/dap/dap/3.2.xsd>* where, again,
the last part of the pathname will reflect the version.

Here's the current stuff that appears in a 3.2 DDX's *Dataset* element:

    <Dataset name="fnoc1.nc"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://xml.opendap.org/ns/DAP/3.2#  http://xml.opendap.org/dap/dap/3.2.xsd"
    ns="http://xml.opendap.org/ns/DAP/3.2#"
    xmlns:dap="http://xml.opendap.org/ns/DAP/3.2#"
    dap_version="3.2">

Why have *.../DAP/3.2#* appear twice? Because this sets the default
namespace to DAP 3.2 and sets the prefix *dap* to that as well. The
default namespace is not used by XML attributes, only elements, so
defining the symbol *dap* as a shorthand provides an easy way to place
attributes is a given namespace.

The *schemaLocation* attribute is used by conforming validating parsers
but is not used to assign namespaces for the elements and attributes in
the document, so the information is repeated.

### Added the *Data* element to ease defining the *DataDDX* response

See the design/discussion in [DataDDX](DataDDX "wikilink"). The *Data*
element is essentially the old BLOB element, but I'm calling it 'Data'
this time ;-)

### Suggestions

Adopt the use of MIME that is used by WCS 1.1.

Modify Attributes so that they appear 'anywhere' in the DDX and
eliminate the *Attribute* element. At the same time, every Attribute
must be in a namespace to bind that attribute to some semantic context.
We will define an 'opendap' namespace for stuff that's not clearly part
of CF, FGDC, et cetera.

[DDX](Category:Development "wikilink") [DDX](Category:DAP4 "wikilink")