Back: [AIS Using NcML](AIS_Using_NcML "wikilink")

Since we are interested in only the part of NcML which adds attributes
and variables to a data set, build a parser for those parts of NcML and
ignore the parts for aggregation.

The [NcML
schema](http://www.unidata.ucar.edu/software/netcdf/ncml/v2.2/AnnotatedSchema.html).

## How to treat NcML elements relative to the DAP

NcML can be used to both add new attributes to existing variables in a
data set, add new variables to a data set, create new data sets and
define aggregations of any of these three this (exiting variables, new
variables or new data sets). For the purposes of the NcML AIS for Hyrax,
the initial version of this handler will be limited to adding attributes
to existing variables in existing data sets. Subsequent versions will
add support for defining new variables and new data sets (really those
two capabilities are very similar) and a separate project will add
support for NcML's *<aggregation>* element.

In the following subsections, each of the NcML elements are described
with respect to how they should be interpreted by this handler. The
elements are: **<netcdf>**, **<readMetadata>**, **<explicit>**, <group>,
**<variable>**, <dimension>, **<attribute>**, <remove>. Those listed in
bold must be handled by the first version of the handler. Note that
either *<readMetadata>* or *<explicit>* must be given, but never both.

### netcdf

The design of NcML is such that using it as an AIS - augmenting an
existing dataset - is really exploiting one of its optional features.
When the *<netcdf>* element has a *netcdf@localtion* attribute, then the
NcML file is providing information that will augment the stuff in the
data set named by *location*. The original NcML design intended for
*location* to name a netCDF file, but we are going to generalize that to
be any file served by the BES running this handler (for *location* that
is a *<file:/>* URL) or any remote Hyrax server (when *location* is a
DAP URL).

In most uses foreseen for this handler, the *netcdf@location* attribute
will always be present.

**In the initial version of the handler, we might require this attribute
be present and we might not allow new variables to be defined, and
instead only allow attributes to be specified for existing variables.**

### readMetadata

If present read the metadata from the data source and augment with the
information supplied in this file. This will normally be present.

### explicit

If present, retain only the variables from the data set named by
*netcdf@location* and then add the information included here.

### group

**A group will be ignored until we start supporting Groups in DAP (DAP
3.4?).**

### variable

The the *<variable>* element will be used to represent any of the scalar
and array types of Bytes, integers, floats and strings/URLs. Note that
the NcML *<variable>* element has a *variable@type* attribute and that
attribute can have *structure* as its value. Form this handler we will
use *<variable type="structure">* elements to represent DAP Structures,
Grids and Sequences

### dimension

Dimensions are part of the netCDF common data model and bind a name with
a size (but not a type). These will be part of DAP 3.3(?) but are not
currently part of our software. When a dimension element is processed,
it will be used to create a binding between the name and the size (an
integer) and any used of that name in a 'dimension context' will be
treated as the same as the integer size. In practice, dimensions are
used to declare the sizes of an Array with the additional notion that
all variables that use a particular dimension are related (as in a DAP
Grid). However, in a data set served by DAP, an existing Grid will
already be explicit, so the dimension element is not needed to establish
this relation.

**Because the *<dimension>* element encodes information that is
redundant for a DAP2--3.2 data source, we might not support it in the
initial version of this handler.**

### attribute

The *<attribute>* element is used to add new attributes, or replace
existing attributes, of a variable.

### remove

The *<remove>* element is used to remove attributes from a variable.

### Notes on NcML

1.  NcML considers everything to have *rank* and thus does not name a
    special constructor type *Array*. Instead scalars have rank zero,
    arrays have rank greater than zero. A rank of one or more is denoted
    using *variable@shape* which lists the dimension names. This
    difference is syntactic only, but worth keeping in mind.
2.  Expand *variable@shape* so that it can contain integer dimension
    sizes in addition to names. This is the case in the code at Unidata,
    but it has not made it into the schema yet (April 2009).
3.  Although not captured by the schema, it appears that a NcML file
    that modifies the attributes of an existing variable does not have
    to specify either the *variable@type* or *variable@shape*
    attributes. The *variable@type* and *@shape* attributes might only
    come into play when/if we use NcML/AIS to add new variables to the
    data set or to define a complete data set (without referencing an
    existing data set as a base to build onto). \[mjohnson: this is
    basically what I did... if not required for an operation, lack of
    specification of type and shape means "use whatever is in the
    underlying data if the name exists"\]
4.  The NcML 2.2 schema uses one *DataType* (\<xsd:simpleType
    name="DataType"\>) for both variables and attributes; we can use the
    *Structure* data type value for attribute containers. \[mjohnson: I
    have done this in current parser, so that if
    attribute@type==Structure, it refers to an attribute container and
    the values must be empty. The current NcML 2.2 schema also fails to
    encapsulate nested attribute trees, but I allow them in our parser.
    \]
5.  NcML does not have an *otherXML* *attribute@type* so we'll have to
    add that. Maybe we can overload the *attribute@shape* attribute so
    that it has the special dimension name *otherXML*? This idea will
    make purists gag, and rightly so, but it might be a good way to try
    NcML out without changing the design at all.

Longer-term:

1.  Separate the *attribute* and *variable* element types so that
    there's a different type (xsd:simpleType) for each.
2.  Add Grid and Sequence to the set of types for a *variable* element
3.  Add *otherXML* to the set of types for *attribute*.

### Notes on parser construction techniques

I would favor building a SAX parser because I think that for an initial
version of the handler, only the *<netcdf>* node will need to be parsed.
Once that's done, a SAX parser is still the way to go because we don't
need the information from the NcML doc to be examined more than once.
What's likely is that as the NcML is parsed the C++ DDS object will be
modified. Once the NcML document has been parsed once, the handler is
not going to need it again.