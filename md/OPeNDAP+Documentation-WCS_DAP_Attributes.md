# Page Superseded

Changes discussed here have coalesced into this page: [DAP4 Attribute
Model](DAP4_Attribute_Model "wikilink")

# Background

Currently the DAP divides metadata into 2 categories: syntactic and
semantic.

- **Syntactic** metadata describes the structure (shape), type, and
  names of the data. It is the minimum information required to work with
  the data in a computer. It is required by the DAP.

<!-- -->

- **Semantic** metadata is any metadata that is not part of the
  syntactic metadata. The DAP attempts to be (semantic) metadata
  agnostic. If there additional metadata the DAP transmits it as
  "Attribute" objects. In DAP2 there is DAS document that contains that
  information. In the DDX there are <Attribute> elements. Attributes are
  tuples: name, type, and value, or containers.

When I developed the current DDX I tried to emulate the DAS as closely
as possible. Thus the Attribute syntax in the current DDX:

``` xml
    <Attribute name="NC_GLOBAL" type="Container">
        <Attribute name="base_time" type="String">
            <value>"88-245-00:00:00"</value>
        </Attribute>
        <Attribute name="title" type="String">
            <value>" FNOC UV wind components from 1988-245 to 1988-247."</value>
        </Attribute>
    </Attribute>
```

# The Issue

The suggestion has been made here:
<http://cf-pcmdi.llnl.gov/trac/ticket/27>

And developed in offline emails that Attributes should really be
connected to conventions (such as COARDS, CF, etc.) whenever possible.
This leads to the need to encode those connections in the DDX (and other
places).

# Disussion

One way to do this is to use (XML) namespaces to associate the
Attributes with the conventions to which they belong.

A possible way to make this association would be to encode the namespace
prefix into the Attribute name. This requires no changes to the DAP, it
just a usage issue.

However - I think the way that Attributes (Which are really just
supposed to be containers or tuples of metadata) are handled in the DDX
is both awkward, and in some ways out of line with the DAP premise that
only the syntactic metadata is "controlled".

Another way to utilize namespaces would be to let the DDX be more
"porous": Right now the DDX is a tightly controlled document, the schema
only allows things in the Dataset element that are in the DAP2
namespace.

But what if we relaxed the schema? What if we allow ANY other XML, as
long as the XML in the DAP2 namespace confirms to the schema and DAP
rules? (*I mention the DAP rules separately from the schema because I
know that the current schema is not complete: A document that
successfully validates against the dap2.xsd schema may not be a legal
DDX. When I wrote the schema I couldn't figure out how to encode all of
the DAP rules into it. I think it's possible that we could make the
schema DAP rule complete, but I don't know for sure. Currently the rest
of the DAP rules are enforced by the Java class that reads the DDX. I
think the same is true in the C++ implementation.*)

Maybe something like this:

<?xml version="1.0" encoding="UTF-8"?>

<Dataset name="ECMWF_ERA-40_subset.nc"
          xmlns:cf="http://iridl.ldeo.columbia.edu/ontologies/cf-att.owl#"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          ns="http://xml.opendap.org/ns/DAP2"
          xsi:schemaLocation="http://xml.opendap.org/ns/DAP2  http://xml.opendap.org/dap/dap2.xsd">
`   `<cf:Conventions rdf:datatype="http://www.w3.org/2001/XMLSchema#string">`CF-1.0`</cf:Conventions>
`   `<cf:history rdf:datatype="http://www.w3.org/2001/XMLSchema#string">`2004-09-15 17:04:29 GMT by mars2netcdf-0.92`</cf:history>
`    `<Array name="longitude">
`        `<Float32/>
`        `<dimension name="longitude" size="144"/>
`        `<cf:long_name rdf:datatype="http://www.w3.org/2001/XMLSchema#string">`longitude`</cf:long_name>
`        `<cf:units rdf:datatype="http://www.w3.org/2001/XMLSchema#string">`degrees_east`</cf:units>
`    `</Array>
`    `<Array name="latitude">
`        `<cf:long_name rdf:datatype="http://www.w3.org/2001/XMLSchema#string">`latitude`</cf:long_name>
`        `<cf:units rdf:datatype="http://www.w3.org/2001/XMLSchema#string">`degrees_north`</cf:units>`        `<Float32/>
`        `<Float32/>
`        `<dimension name="latitude" size="73"/>
`    `</Array>
`    `<Array name="time">
`        `<cf:long_name rdf:datatype="http://www.w3.org/2001/XMLSchema#string">`time`</cf:long_name>
`        `<cf:units rdf:datatype="http://www.w3.org/2001/XMLSchema#string">`hours since 1900-01-01 00:00:0.0`</cf:units>
`        `<Int32/>
`        `<dimension name="time" size="62"/>
`    `</Array>
</Dataset>

I think it's even possible to imagine a dap2:Dataset element embedded in
a larger more complex document. Using XSL or JDOM (and surely many other
XML API's) it is relatively easy to find all of the dap2:Dataset
elements. Once identified it is again easy to traverse their content
looking ONLY at the dap2 namespace to extract the syntactic information
necessary to construct DAP data objects in memory. Each DAP object in
memory could carry references to the non dap2 elements embedded within
it.

**pros**:

- Allow metadata elements to be children of DAP elements - thus
  providing metadata associations with different variables.
- Allow metadata elements to be clearly associated with the namespace of
  the convention to which they conform.

**cons**:

- Might be challenging to implement the DAS API for this.

It may be that allowing any XML anywhere inside the dap2 elements is too
relaxed. We may wish to bound it with some rules. For example we
probably don't want to allow people to add stuff to Array template
variables... Unless they are constructor types.

# Some Details

## Local Attribute Namespace

We define a default "local" namespace into which we can put attribute
metadata that isn't otherwise associated with a convention or namespace.

Local Attrbute Namespace
dap2:Dataset/@xml:base + "/att#" = http://url/to/the/document.ddx/att#

:\* **The dap2:Dataset element (the root element of the DDX) needs to
contain an xml:base attribute. (This should become a trac ticket)**

## Provenance

Modifications should be tracked through a provenance record. Since the
morphing of the metadata is essentially an AIS like activity I propose
we create a DAP AIS namespace (something like:
http://xml.opendap.org/ns/DAP2#ais ??) to use for the provenance record.

\>**ndp: Do we want to add a provenance record for moving the
<dap:2Attribute> elements to the *local* attribute namespace?**

(nathan - I think yes)

(benno - I thought not, mainly because I thought it was lossless. Also,
I was mentally focusing on the RDF version, which will always be this
way, i.e. it does not need the other representation of attributes. Could
we proceed with this in the RDF version so that I can try the crosswalks
for the AIS?)

### Example

Consider ths set of DAP \<Attribute elements:

`   `<Attribute name="NC_GLOBAL" type="Container">
`       `<Attribute name="Conventions" type="String">
`           `<value>`"CF-1.0"`</value>
`       `</Attribute>
`       `<Attribute name="history" type="String">
`           `<value>`"2004-09-15 17:04:29 GMT by mars2netcdf-0.92"`</value>
`       `</Attribute>
`   `</Attribute>`        `
`   `<Attribute name="units" type="String">
`           `<value>`"degrees_east"`</value>
`       `</Attribute>
`       `<Attribute name="long_name" type="String">
`           `<value>`"longitude"`</value>
`   `</Attribute>

Mapping them to the *local* namespace:

`   `<att:NC_GLOBAL>
`       `<att:Conventions rdf:datatype="http://www.opendap.org/ns/DAP2#String">`"CF-1.0"`</att:Conventions>
`       `<att:history rdf:datatype="http://www.opendap.org/ns/DAP2#String">`"2004-09-15 17:04:29 GMT by mars2netcdf-0.92"`</att:history >
`   `</att:NC_GLOBAL >
`   `<att:units rdf:datatype="http://www.opendap.org/ns/DAP2#String">`"2004-09-15 17:04:29 GMT by mars2netcdf-0.92"`</att:units >
`   `<att:long_name rdf:datatype="http://www.opendap.org/ns/DAP2#String">`"2004-09-15 17:04:29 GMT by mars2netcdf-0.92"`</att:long_name >

Applying an AIS filter (for the CF convention) should produce:

`   `<cf:Conventions rdf:datatype="http://www.opendap.org/ns/DAP2#String">`"CF-1.0"`</cf:Conventions>
`   `<cf:history rdf:datatype="http://www.opendap.org/ns/DAP2#String">`"2004-09-15 17:04:29 GMT by mars2netcdf-0.92"`</cf:history >
`   `<cf:units rdf:datatype="http://www.opendap.org/ns/DAP2#String">`"2004-09-15 17:04:29 GMT by mars2netcdf-0.92"`</cf:units >
`   `<cf:long_name rdf:datatype="http://www.opendap.org/ns/DAP2#String">`"2004-09-15 17:04:29 GMT by mars2netcdf-0.92"`</cf:long_name >

And AIS changes are added to the dataset:

<ais:change>
`    `<ais:sourceElement xlink:type="simple" xlink:href="#xpointer(/Dataset/att:NC_GLOBAL/att:Conventions)" />
`    `<ais:targetElement xlink:type="simple" xlink:href="#xpointer(/Dataset/cf:Conventions)" />` `
</ais:change>
<ais:change>
`    `<ais:sourceElement xlink:type="simple" xlink:href="#xpointer(/Dataset/att:NC_GLOBAL/att:history)" />
`    `<ais:targetElement xlink:type="simple" xlink:href="#xpointer(/Dataset/cf:history)" />
</ais:change>
<ais:change>
`    `<ais:sourceElement xlink:type="simple" xlink:href="#xpointer(/Dataset/att:units)" />
`    `<ais:targetElement xlink:type="simple" xlink:href="#xpointer(/Dataset/cf:units)" />
</ais:change>
<ais:change>
`    `<ais:sourceElement xlink:type="simple" xlink:href="#xpointer(/Dataset/att:long_name)" />
`    `<ais:targetElement xlink:type="simple" xlink:href="#xpointer(/Dataset/cf:long_name)" />
</ais:change>

(benno) There are two ways we could document such changes: 1) that we
changed particular statements to particular other statements, or 2) we
changed a class of statements to another class of statements. I would
say the above is an example of 1). To change it to 2), let's use an
alternative property ais:changeClass. Namespace'd elements are URIs, so
we will not be making an xpointer reference but we will be using the
URI. This is slightly complicated by the fact that XML does not do
namespace expansion for href values, but that can be fixed by defining
entities. So the documentation suggested becomes

<ais:changeClass>
`    `<ais:sourceElement xlink:type="simple" xlink:href="&att;Conventions" />
`    `<ais:targetElement xlink:type="simple" xlink:href="&cf;Conventions" />
</ais:changeClass>
<ais:changeClass>
`    `<ais:sourceElement xlink:type="simple" xlink:href="&att;history" />
`    `<ais:targetElement xlink:type="simple" xlink:href="&cf;history" />
</ais:changeClass>
<ais:changeClass>
`    `<ais:sourceElement xlink:type="simple" xlink:href="&att;units" />
`    `<ais:targetElement xlink:type="simple" xlink:href="&cf;units" />
</ais:changeClass>
<ais:changeClass>
`    `<ais:sourceElement xlink:type="simple" xlink:href="&att;long_name" />
`    `<ais:targetElement xlink:type="simple" xlink:href="&cf;long_name" />
</ais:changeClass>

where we have defined entities that correspond to each namespace, e.g.

<?xml version="1.0" encoding="UTF-8"?>

`<!DOCTYPE DAP2 [`
`<!ENTITY cf "`[`http://iridl.ldeo.columbia.edu/ontologies/cf-att.owl#`](http://iridl.ldeo.columbia.edu/ontologies/cf-att.owl#)`">`
`<!ENTITY att "dataseturl.att#">`
`]>`
<Dataset name="ECMWF_ERA-40_subset.nc"
 xmlns:cf="http://iridl.ldeo.columbia.edu/ontologies/cf-att.owl#"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 ns="http://xml.opendap.org/ns/DAP2"
 xsi:schemaLocation="http://xml.opendap.org/ns/DAP2  http://xml.opendap.org/dap/dap2.xsd">

`   `<cf:Conventions rdf:datatype="http://www.w3.org/2001/XMLSchema#string">`CF-1.0`</cf:Conventions>
`   `<cf:history rdf:datatype="http://www.w3.org/2001/XMLSchema#string">`2004-09-15 17:04:29 GMT by mars2netcdf-0.92`</cf:history>

`   `<Array name="longitude">
`       `<Float32/>
`       `<dimension name="longitude" size="144"/>
`       `<cf:long_name rdf:datatype="http://www.w3.org/2001/XMLSchema#string">`longitude`</cf:long_name>
`       `<cf:units rdf:datatype="http://www.w3.org/2001/XMLSchema#string">`degrees_east`</cf:units>
`   `</Array>
`   `<Array name="latitude">
`       `<cf:long_name rdf:datatype="http://www.w3.org/2001/XMLSchema#string">`latitude`</cf:long_name>
`       `<cf:units rdf:datatype="http://www.w3.org/2001/XMLSchema#string">`degrees_north`</cf:units>`        `<Float32/>
`       `<Float32/>
`       `<dimension name="latitude" size="73"/>
`   `</Array>
`   `<Array name="time">
`       `<cf:long_name rdf:datatype="http://www.w3.org/2001/XMLSchema#string">`time`</cf:long_name>
`       `<cf:units rdf:datatype="http://www.w3.org/2001/XMLSchema#string">`hours since 1900-01-01 00:00:0.0`</cf:units>
`       `<Int32/>
`       `<dimension name="time" size="62"/>
`   `</Array>
</Dataset>

Encoding 2) in RDF is nothing special. Encoding 1) (documentation of
particular statements) requires using an little-used part of RDF/XML
called reification, but is doable. For example, if we had RDF/XML that
included both the before and after,

`  `<dap:A_Float32 rdf:ID="longitude">
`    `<att:long_name rdf:datatype="http://www.w3.org/2001/XMLSchema#string">`longitude`</cf:long_name>
`    `<att:units rdf:datatype="http://www.w3.org/2001/XMLSchema#string">`degrees_east`</cf:units>
`    `<cf:long_name rdf:datatype="http://www.w3.org/2001/XMLSchema#string">`longitude`</cf:long_name>
`    `<cf:units rdf:datatype="http://www.w3.org/2001/XMLSchema#string">`degrees_east`</cf:units>
`  `</dap:A_Float32>`     `
`     `

We could reify the statements as follows

`  `<dap:A_Float32 rdf:ID="longitude">
`    `<att:long_name rdf:ID="longitude_att_long_name" rdf:datatype="http://www.w3.org/2001/XMLSchema#string">`longitude`</cf:long_name>
`    `<att:units rdf:ID="longitude_att_units" rdf:datatype="http://www.w3.org/2001/XMLSchema#string">`degrees_east`</cf:units>
`    `<cf:long_name rdf:ID="longitude_cf_long_name" rdf:datatype="http://www.w3.org/2001/XMLSchema#string">`longitude`</cf:long_name>
`    `<cf:units rdf:ID="longitude_cf_units" rdf:datatype="http://www.w3.org/2001/XMLSchema#string">`degrees_east`</cf:units>
`  `</dap:A_Float32>`     `
`  `<rdf:Description rdf:about="#longitude_cf_long_name">
`    `<ais:changedFrom rdf:resource="#longitude_att_long_name" />
`    `<dc:creator rdf:resource="http://opendap.org/superCFfixer" />
`  `</rdf:Description>
`  `<rdf:Description rdf:about="#longitude_cf_units">
`    `<ais:changedFrom rdf:resource="#longitude_att_units" />
`    `<dc:creator rdf:resource="http://opendap.org/superCFfixer" />
`  `</rdf:Description>

where I added a dublin core reference for the program responsible as
well (would need to add dc to the namespaces). It is not clear to me
that we want to do statement-by-statement provenence (i.e. 1 instead of
2), but something like this would work in RDF. One could also document
ais:changedFrom as a subproperty of a Dublin Core relation, so that the
relationship could be understood in a more general way without losing
any specificity. On the other hand, when we apply the "superCFfixer" we
can certainly should a statement to the dataset/ontology that we
processed it, e.g.

`   `<dap:Dataset>
`     `<ais:processedBy rdf:resource="http://opendap.org/superCFfixer" />
`   ...`
`   `</dap:Dataset>

or

`  `<owl:Ontology>
`      `<ais:procssedBy rdf:resource="http://opendap.org/superCFfixer" />
`    `</owl:Ontology>

Note that while we need the original statements around so that they can
be connected to the derived statements, we can satisfy that with a
reference to the original file (owl:imports or rdf:SeeAlso). Ideally we
would like to factor out the provenence so that we can serve the content
without it upon request.

\>**ndp: Benno - Is the second provenance method (where we use
<ais:changeClass> elements) the preferred one? ?**

------------------------------------------------------------------------

------------------------------------------------------------------------

# The Plan (Let's keep changing this until it's the thing we are going to do)

## 1. DAP 2.1 Schema

- Change the DDX so that we allow other XML from other namespaces.
- Change the representation of Attributes to a tuple defined in the
  local namespace.

For example:

`   `<Attribute name="NC_GLOBAL" type="Container">
`       `<Attribute name="Conventions" type="String">
`           `<value>`"CF-1.0"`</value>
`       `</Attribute>
`       `<Attribute name="history" type="String">
`           `<value>`"2004-09-15 17:04:29 GMT by mars2netcdf-0.92"`</value>
`       `</Attribute>
`   `</Attribute>`        `
`   `<Attribute name="units" type="String">
`           `<value>`"degrees_east"`</value>
`       `</Attribute>
`       `<Attribute name="long_name" type="String">
`           `<value>`"longitude"`</value>
`   `</Attribute>

Becomes:

`   `<att:NC_GLOBAL>
`       `<att:Conventions rdf:datatype="http://www.opendap.org/ns/DAP2#String">`"CF-1.0"`</att:Conventions>
`       `<att:history rdf:datatype="http://www.opendap.org/ns/DAP2#String">`"2004-09-15 17:04:29 GMT by mars2netcdf-0.92"`</att:history >
`   `</att:NC_GLOBAL >
`   `<att:units rdf:datatype="http://www.opendap.org/ns/DAP2#String">`"2004-09-15 17:04:29 GMT by mars2netcdf-0.92"`</att:units >
`   `<att:long_name rdf:datatype="http://www.opendap.org/ns/DAP2#String">`"2004-09-15 17:04:29 GMT by mars2netcdf-0.92"`</att:long_name >

Where att is the namespace prefix for the local namespace (the XML
base + /attributes = <http://document.ddx/att> )

*I used the DAP2 schema to define the types of the tuples. That is
subject to change based on the answers to questions below*

[jimg](User:Jimg "wikilink") 14:06, 10 December 2008 (PST) I'd like to
keep the wordiness of the DDX to a dull roar (it's XML afterall...) so
I'd like to *not* use rdf:datatype but instead us
*type="\<<DAP Attribute type name>\>"*. The RDF transformation code
(i.e., XSLT in the OLFS) can handle making the change to the RDF type.

## 2. BES Handler

Currently the BES handlers produce the DDX response.

It may be worthwhile considering having these handlers perform one or
more of the following:

1.  Move the current <dap2:Attribute> elements (not explicitly
    associated with a name space) to the local (aka dataset) attribute
    namespace. If the data source provides attribute information that is
    already mapped to a namespace then it should go there.(This is a
    moot point right now because I don't think there is such a data
    source.) Otherwise it gets put in the local attribute namespace. So
    the DDX that gets produced is a DAP2.1 DDX that has attributes as
    above.
2.  Use handler specific information to map the <dap2:Attribute>
    elements into a more spcifc namespace associated with some external
    project/standard.

## 3. AIS filter

Somewhere between the BES handler and the client the DDX is passed
through zero or more AIS like operations each of which attempts to
reorganize the metadata to some convention or standard.

The most basic AIS filter will convert <dap2:Attribute> elements to
elements in the *local* attribute namespace. Subsequent AIS filtes will
assign stuff in the local namespace to a "convention" namespace. (Can we
call this *promotion*?)

*'Questions:*

1.  So do we make the AIS filters to be associated with the conventions,
    or with the data source file type?

<!-- -->

1.  Is there an AIS filter for COARDS and for CF? Or, is there an AIS
    filter for NetCDF and and one for HDF4?

<!-- -->

1.  What is the mapping, for each dap2 attribute data type:

namespace xsd=<http://www.w3.org/2001/XMLSchema#> alternatively an
entity.


    Byte     -->  &xsd;byte
    Int16    -->  &xsd;short
    UInt16   -->  &xsd;unsignedShort
    Int32    -->  &xsd;long
    UInt32   -->  &xsd;unsignedLong
    Float32  -->  &xsd;float
    Float64  -->  &xsd;double
    String   -->  &xsd;string
    URL      -->  &xsd;anyURI

RDF uses xsd: for typing literals. Note that xsd also has
&xsd;unsignedByte.

### Basic AIS Filter

Changes a current (DAP2) DDX to the proposed DAP2.1 DDX.

For example:

`   `<Attribute name="NC_GLOBAL" type="Container">
`       `<Attribute name="Conventions" type="String">
`           `<value>`"CF-1.0"`</value>
`       `</Attribute>
`       `<Attribute name="history" type="String">
`           `<value>`"2004-09-15 17:04:29 GMT by mars2netcdf-0.92"`</value>
`       `</Attribute>
`   `</Attribute>`        `
`   `<Attribute name="units" type="String">
`           `<value>`"degrees_east"`</value>
`       `</Attribute>
`       `<Attribute name="long_name" type="String">
`           `<value>`"longitude"`</value>
`   `</Attribute>

Becomes:

`   `<att:NC_GLOBAL>
`       `<att:Conventions rdf:datatype="http://www.opendap.org/ns/DAP2#String">`"CF-1.0"`</att:Conventions>
`       `<att:history rdf:datatype="http://www.opendap.org/ns/DAP2#String">`"2004-09-15 17:04:29 GMT by mars2netcdf-0.92"`</att:history >
`   `</att:NC_GLOBAL >
`   `<att:units rdf:datatype="http://www.opendap.org/ns/DAP2#String">`"2004-09-15 17:04:29 GMT by mars2netcdf-0.92"`</att:units >
`   `<att:long_name rdf:datatype="http://www.opendap.org/ns/DAP2#String">`"2004-09-15 17:04:29 GMT by mars2netcdf-0.92"`</att:long_name >

(Again, I used the DAP2 schema to define the types of the tuples. We
need to fill out the table in the questions section above to get the
types.)

The provenance for this change would look like:

    <ais:changeClass>
        <ais:sourceElement xlink:type="simple" xlink:href="&dap2;Attribute" />
        <ais:targetElement xlink:type="simple" xlink:href="&att;Conventions" />
    </ais:changeClass>
    <ais:changeClass>
        <ais:sourceElement xlink:type="simple" xlink:href="&dap2;Attribute" />
        <ais:targetElement xlink:type="simple" xlink:href="&att;history" />
    </ais:changeClass>
    <ais:changeClass>
        <ais:sourceElement xlink:type="simple" xlink:href="&dap2;Attribute" />
        <ais:sourceElement xlink:type="simple" xlink:href="&att;units" />
    </ais:changeClass>
    <ais:changeClass>
        <ais:sourceElement xlink:type="simple" xlink:href="&dap2;Attribute" />
        <ais:sourceElement xlink:type="simple" xlink:href="&att;long_name" />
    </ais:changeClass>

### CF (Or is it netcdf??) Conventions AIS Filter

This AIS filter must interpret the local attributes and where possible
and reasonable associate them with the