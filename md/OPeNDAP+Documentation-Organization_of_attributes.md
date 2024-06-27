## Background

From: benno@iri.columbia.edu
Subject: DAP 4.0 Schema
Date: May 12, 2005 8:50:02 AM MDT
To: dods-tech@unidata.ucar.edu
My apologies for making these comments so late: I was trying to monitor
the DAP 4.0 progress, but the document I was looking at did not mention
the schema, and somehow I missed it when the document got improved.

Essentially DAP 4.0 as it currently stands uses XML to carry an improved
dataset with attributes structure not unlike DAP 2.0. In particular, it
defines an attribute cell which has a name and type and contents. This
is awfully close to defining a structure within XML to carry the exact
same information that XML is designed to carry (shades of GMT and netcdf
...).

I was thinking that it would be awfully nice if a set of attributes that
belong to a convention (say CF) got translated to a set of XML tags with
a CF schema, i.e. the tags would be defined in a namespace CF. So we
would see

<dataset name=test xmlns:cf="http://unidata.ucar.edu/2005/CF">
`    `<cf:title>`A wonderful test dataset`</cf:title>
`    `<cf:institution>`the great beyond`</cf:institution>
`    `<cf:references>`dods-tech@unidata.ucar.edu`<cf:references>
`     ...`
</dataset>

This way we would truly be using XML to transmit the information. The
implication of this is that we would split the DAP schema into two
parts: a organizational part (datasets and variables), and a concrete
data-typing part (most of the rest of the current DAP4.0 schema). The
DAP core would only use the concrete data-typing part, (except maybe for
the nesting of the organizational part to create dot-separated names in
the constraints, though in this example that is explicit in the Blob url
so perhaps it is not even an issue).

     <variable name=sst>
         <cf:long_name>sea surface temperature</cf:long_name>
         <cf:units>Celsius_scale</cf:units>
         <dap:Array>
             <dap:Float32/>
             <dap:dimension size="16" name="latitude"/>
             <dap:dimension size="17" name="longitude"/>
             <dap:dimension size="21" name="time"/>
         </dap:Array>
         <dap:Blob URL="http://dcz.opendap.org/dap/data/nc/fnoc1.nc?u"/>
     </variable>

This is only a rough idea -- there are others out there that could make
better syntactic choices (like a single dap container that resets the
default name space for its contents), I am just trying to make a general
point. The reason for doing this is two-fold: 1) the core core could be
simpler and more stable, because almost everything outside the hopefully
small core namespace would not affect the OpenDAP core, and 2) OpenDAP
xml could simply be inserted in an XML document following whatever
specific metadata conventions and data structures to create a document
with accessible data. (GML perhaps?) It seems pretty clear that the
future is metadata transmission in XML (with many different standards
for metadata), and OpenDAP has always been a transmission mechanism that
avoided constraining the metadata, it would be really nice to turn over
the metadata responsibility to XML.

Benno

------------------------------------------------------------------------

Added to this wiki: [jimg](User:Jimg "wikilink") 14:06, 24 June 2008
(PDT)

## More ideas about this syntax

Some other ideas; a mixture of syntaxes:

``` xml
 <variable name="sst">
     <cf:long_name>sea surface temperature</cf:long_name>
     <cf:units>Celsius_scale</cf:units>

     <dap:attribute name="long_name" type="string">sea surface temperature</dap:attribute >

     <dap:attribute name="coefs" type="float64"><value>0.01</value> <value>-1.5</value> </dap:attribute>

     <att:units type="string">Celsius_scale</att:units >
     <att:foo type="string">bar</att:foo>

     <dap:Array>
         <dap:Float32/>
         <dap:dimension size="16" name="latitude"/>
         <dap:dimension size="17" name="longitude"/>
         <dap:dimension size="21" name="time"/>
     </dap:Array>
 </variable>
```

But, the question I would like to understand better is **How does having
an XML document with a potentially infinite number of elements affect
systems?** That is, when we take an attribute from a netcdf file (or any
other file type...) and make an element name from the attribute's name,
since there's no limit to the names of attributes, there's a potentially
infinite numer of element names in the DDX.

Other issues:

1.  It's possible to have attributes in netcdf (and other?) formats that
    have colons in their names. That means that when a handler sees
    these names, it must *remove the colon* in order to make that
    attribute name an element name that's legal XML.
2.  There are other characters that are not allowed in QNames, and these
    would all have to be removed for this attribute-name --\>
    element-name scheme to work in general.
3.  Is it realistic to think that a handler (netcdf or otherwise) is
    going to be rewritten in such a way that it can distinguish which
    dap:Attribute@name values that contain colons need to be interpreted
    as namespace prefixed names vs which ones that contain colons must
    have their colons removed/changed so that they become a vaild QName?
    (If you do have modify it how do you accurately store the real name
    of the element? Add an attribute called "name" to store the Name? At
    that point we're back to where we were with dap:Attribute)

An alternative is to use the existing DDX syntax but add an optional
*namespace* attribute which provides the handler with an opportunity to
say it knows this attribute belongs to a particular namespace. The
handler would have to take care of assigning a prefix for the namespace
in many situations - work out the details...

## First Proposed alternative syntax

[jimg](User:Jimg "wikilink") 15:26, 17 January 2009 (PST) **Make sure to
read the "second proposed alternative syntax" since that's the one we're
going to use. This is here to capture the complete discussion (and
because it's what we thougt abut first)**

*The idea behind all of this is to have the handlers build responses
that can include information about the conventions/standards followed by
the dataset*

[jimg](User:Jimg "wikilink") 09:57, 14 January 2009 (PST) read this over
but also see below where I have a 'Second Proposed Alternative syntax'

### Discussion

The current (DAP 3.2) DDX syntax for attributes looks like:

        <Grid name="temp">
            <Attribute name="long_name" type="String">
                <value>temperature</value>
            </Attribute>
            <Attribute name="units" type="String">
                <value>K</value>
            </Attribute>
            <Attribute name="grid_mapping" type="String">
                <value>crs</value>
            </Attribute>
            <Attribute name="wcs:gridCRS" type="String">
                <value>crs</value>
            </Attribute>
            <Array name="temp">
            ...

And I'd suggest that we adopt the following addition to that syntax:
That the Attribute element support an optional *namespace* xml attribute
(dap:attribute@namespace) and that xml attribute be used to indicate
that a particular dap:attribute belongs to a given convention/standard.
Like this:

<Attribute name="long_name" type="string" nsPrefix="cf">
<Attribute name="long_name" type="string" namespace="http://ns.ucar.edu/xml/ns/cf-1.0">

where we would declare the string "cf" to be a namespace prefix in the
usual XML way, or simply use the namespace attribute to inject the full
namespace name

Pro:

1.  This will be possible to implement in our handlers. We will not have
    to divine new names for data set attributes that contain characters
    that are not allowed in QNames.
2.  The dap:attribute@namespace is a bit of a hack, but it's a
    recognizable one that should provide the needed information to XSLT
    and other code
3.  encoding the namespace & its prefix (i.e., "cf" in this example)
    using the XML notation for such a beast is something that the
    handler can do and that XSLT and otehr code should also be able to
    access

Con:

1.  In order to get the attributes represented in the form where they
    take on in their namespaces (when attributes are in a namespace;
    e.g. CF-1.0), we will have to transform the DDX using XSLT.

### Analysis

[ndp](User:Ndp "wikilink") 06:56, 16 January 2009 (PST) I reviewed this,
generated some examples, and learned the following:

1.  XML elements may have XML attributes (possibly associated with a
    namespace), contain other elements (possibly associated with a
    namespace) , and text. XML allows multiple elements of the same type
    at the same level in the hierarchy. This is incompatible with the
    current semantics of the dap:Attribute container. IN order to
    encapsulate arbitray XML in dap:Attributes we would need to add 3
    new dap:Attribute@type values: xmlElementNode, xmlAttributeNode, and
    xmlTextNode.
2.  Even with the addition of these 3 new types the resulting
    dap:Attributes break the structural rules for dap:Attribute because
    there will be many cases in which there are multiple dap:Attribute
    elements with the same @name value at the same level.
3.  **dap:Attribute@type='Container'** Attempting to map xml elements to
    dap:Attributes of type Container has the following identified
    problems:
    - If we don't expressly construct an xmlElementNode dap:Attribute
      type then we are relying on the presence (or lack thereof) of a
      @prefix or @namespace attribute in the dap:Attribute element
      declaration to control the structural interpretation of the
      dap:Attribute element's content.
    - Since this mapping injects the XML elements into a subset of
      possible dap:Attribute Container elements, this means that people
      could easily construct namespace mapped Attribute containers that
      were not promotable to XML because their content doesn't follow
      the semantics of an XML element type.
4.  **dap:Attribute@type='String'** Attempting to map xml text nodes to
    dap:Attributes of type String has the following identified problems:
    - If we don't expressly construct an xmlTextNode dap:Attribute type
      then we are relying on the presence (or lack thereof) of a @prefix
      or @namespace attribute in a parent dap:Attribute element
      declaration to control the structural interpretation of the
      dap:Attribute (@type='String') element's content. IN other words
      only by looking the parent can we know if it's a textNode in an
      XML element.

#### Short example

                <SpatialDomain ns="http://www.opengis.net/wcs/1.1">
                    <ows:BoundingBox gml:crs="urn:ogc:def:crs:EPSG::4326">
                        <ows:LowerCorner>-97.8839 21.736</ows:LowerCorner>
                        <ows:UpperCorner>-57.2312 46.4944</ows:UpperCorner>
                    </ows:BoundingBox>
                </SpatialDomain>

Becomes:

                <dap:Attribute name="SpatialDomain" namespace="http://www.opengis.net/wcs/1.1"
                               type="Container">
                   <dap:Attribute name="BoundingBox" namespace="http://www.opengis.net/ows/1.1" type="Container">
                      <dap:Attribute name="crs" type="xmlAttributeNode" namespace="http://www.opengis.net/gml/3.2">
                         <dap:value>urn:ogc:def:crs:EPSG::4326</dap:value>
                      </dap:Attribute>
                      <dap:Attribute name="LowerCorner" namespace="http://www.opengis.net/ows/1.1" type="Container">
                         <dap:Attribute name="textNode" type="String">
                            <dap:value>-97.8839 21.736</dap:value>
                         </dap:Attribute>
                      </dap:Attribute>
                      <dap:Attribute name="UpperCorner" namespace="http://www.opengis.net/ows/1.1" type="Container">
                         <dap:Attribute name="textNode" type="String">
                            <dap:value>-57.2312 46.4944</dap:value>
                         </dap:Attribute>
                      </dap:Attribute>
                   </dap:Attribute>
                </dap:Attribute>

This adheres to the structural rules of the DAP, but it's interpretation
is implicit (?) (I may mean defined by or something) from the presence
of the @namespace or @prefix attributes.

### Examples using this syntax to map WCS XML to DAP attributes and back using XSLT

Both of these examples rely on the use of two new xml attributes for
dap:Attribute:

- **namespace** - to store the namespace for the xml element.
- **prefix** - to store the namespace prefix for the element. (This is
  probably not very useful in practice given that the XSLT and other
  API's don't really care about the prefix - just the namespace)

#### [Example 1](dap:Attribute_Extension_Example_1 "wikilink")

Here I used a total of six changes to the current DAP Attribute
definitions: Three new dap:Attribute types to encapsulate arbitrary XML
in dap:Attribute elements, and two new xml attributes for the
dap:Attribute element.

1.  type=**'xmlElementNode**'
2.  type=**'xmlAttributeNode**'
3.  type=**'xmlTextNode**'
4.  **'namespace**' attribute
5.  **'prefix** attribute
6.  ***Must allow multiple dap:Attributes of the same name to be
    siblings in the document.***

However, this mapping can only be 1:1 with a subset of the total
dap:Attribute set (and thus never onto). In fact, this transform really
maps arbitrary XML to a brand new part of the dap:Attrbute space would
not exist without the first five of the six changes above.

XML to special dap:Attributes:
**[srcDoc](Dap:Attribute_Extension_Example_1#Source_XML "wikilink")**
-\> **[xmlToDapAtt
XSLT](Dap:Attribute_Extension_Example_1#Transform "wikilink")** -\>
**[dap:Attributes](Dap:Attribute_Extension_Example_1#Result "wikilink")**

Special dap:Attributes to XML:
**[dap:Attributes](Dap:Attribute_Extension_Example_1#Result "wikilink")**
-\> **[dapAttToXML
XSLT](Dap:Attribute_Extension_Example_1#Transform_Back "wikilink")** -\>
**[resultDoc](Dap:Attribute_Extension_Example_1#Result_2 "wikilink")**
(should be same as srcDoc)

[ndp](User:Ndp "wikilink") 09:16, 20 January 2009 (PST) personally I
think the result is awful.

#### [Example 2:](dap:Attribute_Extension_Example_2 "wikilink")

Here I attempt to encapsulate arbitary XML in the dap by limiting the
changes in the dap:Attrubte mode to:

1.  type=**'xmlAttributeNode**' for dap:Attribute
2.  **'namespace**' attribute for dap:Attribute
3.  **'prefix** attribute for dap:Attribute
4.  ***Must allow multiple dap:Attributes of the same name to be
    siblings in the document.***

Again mapping can only be 1:1 with a subset of the total dap:Attribute
set (and thus never onto). And like the previous example, the transform
really maps arbitrary XML to a brand new part of the dap:Attrbute space
would not exist without the first 3 changes above. THis is complicated
by the fact that in order to encapsulate XML text content this examples
relies on context sensitive interpretation of dap:Attributes of type
String.

XML to special dap:Attributes:
**[srcDoc](Dap:Attribute_Extension_Example_2#Source_XML "wikilink")**
-\> **[xmlToDapAtt
XSLT](Dap:Attribute_Extension_Example_2#Transform_.28_xml_-xslt-.3E_dap.29 "wikilink")**
-\>
**[dap:Attributes](Dap:Attribute_Extension_Example_2#Result "wikilink")**

Special dap:Attributes to XML:
**[dap:Attributes](Dap:Attribute_Extension_Example_2#Result "wikilink")**
-\> **[dapAttToXML
XSLT](Dap:Attribute_Extension_Example_2#Transform_Back_.28dap_-xslt-.3E_xml.29 "wikilink")**
-\>
**[resultDoc](Dap:Attribute_Extension_Example_2#Result_2 "wikilink")**
(should be same as srcDoc)

### Development Plan

How to implement this change:

1.  Adopt this syntax for DAP 3.3
2.  Modify the DAP to create for "namespace aware" dap:Attribute
    elements
    1.  Implement new dap:Attribute attributes
        - namepace
        - prefix
    2.  Implement new dap:Attribute type
        - xmlAttributeNode

How this will be used by the WCS interface:

1.  Use an AIS to inject namespace tagged dap:Attribute elements derived
    from wcs:CoverageDescription and wcs:CoverageSummary elements.
2.  This content WILL be in the DAP attribute namespace
3.  The consumers of this DDX (the OLFS for now) must be modified to
    understand the new dap:Attribute syntax and be able to promote the
    namespace associated attributes to their original XML constructs.
4.  The OLFS will collect all of the DDX's in the WCS coverage catalog,
    grab their wcs:CoverageSummary and wcs:CoverageDescription elements
    and use them to build responses for GetCapabilities and
    DescribeCoverage requests.
5.  This leaves the OLFS to parse incoming GetCoverage request and to
    build data access URL's (or errors!) based on their content.

## Second Proposed Alternative Syntax

[jimg](User:Jimg "wikilink") 10:15, 14 January 2009 (PST) OK, I think
this idea combines things, let me know what you all think.

[jimg](User:Jimg "wikilink") 16:22, 14 January 2009 (PST) We've chosen
this syntax!

We could consider changing the DAP 3.2 DDX to allow any XML element to
be present at certain locations in the DDX. These elements might
(probably will) be ignored by most DAP-aware code, but they *will* be
transported by the document. The 'particular place' will be right before
any place where an *Attribute* element may appear.

The schema change might look like this:

`   `<xs:complexType name="BaseType">
`       `<xs:annotation>
`           `<xs:documentation>`DODS Base Type`</xs:documentation>
`       `</xs:annotation>
`       `<xs:sequence>
`           `**<xsd:any namespace="##other" minOccurs="0" maxOccurs="unbounded" processContents="strict"/>**
`           `<xs:element ref="Attribute" minOccurs="0" maxOccurs="unbounded"/>
`           `<xs:element ref="Alias" minOccurs="0" maxOccurs="unbounded"/>
`       `</xs:sequence>
`       `<xs:attribute name="name" type="xs:string"/>
`   `</xs:complexType>

Or, we could demand that arbitrary XML be wrapped in a special type of
dap:Attribute. The schema change for that might looks like this:

`   `<xs:complexType name="DASAttribute">
`       `<xs:annotation>
`           `<xs:documentation>`DAS Attribute Type`</xs:documentation>
`       `</xs:annotation>
`       `<xs:choice>
`           `<xs:sequence>
`               `<xs:element name="value" type="xs:string" maxOccurs="unbounded"/>
`           `</xs:sequence>
`           `<xs:sequence>
`               `**<xsd:any namespace="##other" minOccurs="0" maxOccurs="unbounded" processContents="strict"/>**
`               `<xs:element ref="Attribute" minOccurs="0" maxOccurs="unbounded"/>
`               `<xs:element ref="Alias" minOccurs="0" maxOccurs="unbounded"/>
`           `</xs:sequence>
`       `
`       `</xs:choice>
`       `<xs:attribute name="name" type="xs:string" use="required"/>
`       `<xs:attribute name="type" type="dap2:DataType" use="required"/>
`   `</xs:complexType>

[jimg](User:Jimg "wikilink") 09:56, 14 January 2009 (PST) I don't know
enough about XML Schema to understand what a document would like if it
used this schema. However, **the important thing** about wrapping the
'foreign' XML in a DAP Attribute container is that we can use existing
tools like NCML to inject that foreign XML without changing NCML much.
That's the important thing: Can we do what's proposed without changing
or adding to NCML? We will need to tweak NCML just a little to build new
DAP Attributes that contain foreign XML, but only a very little. If we
want to insert those elements 'naked' then I'll have to move just a bit
farther from the current NCML 2.2. In NCML I'll probably actually use
the 'add a new attribute' syntax and just overload it with a new XML
attribute type like 'xml'.

### Discussion

([jimg](User:Jimg "wikilink") said:) I suggest that we keep the current
DDX syntax for attributes since it save us from having to do text
processing in the handlers.

> The problem with representing all DAP attributes using a syntax like
> \<att:*attribute name* ...\> is that *attribute name* can easily
> contain characters that are not allowed in what XML calls a QName.
> This will be a real problem when we apply this representation to data
> sets which use HDF4 and HDF5. In those you can find examples of
> attributes with colons and other odd characters. So we'd have to adopt
> a syntax like \<att:*modified attribute name* real_name="*attribute
> name*" ...\>.

And that we augment this with the idea that we can inject arbitrary XML
into a DDX anyplace we can also have attributes (Nathan, smooth over the
rough edges here). This way we can represent WCS info in the short term
and we can build handlers that can include elements like \<cf:long_name
...\> all with out having a schema that requires client to
parse/understand a potentially infinite set of elements for DAP.

An example would look like:

    <Grid name="temp">
            <!-- This stuff is what's new. Despite my earlier comments about NCML
                 modifications, I can code this w/o much more effort than I can code
                 a NCML handler in the first place. And of course the handler would
                 use its own knowledge about long_name and units to make the CF elements.
                 We'd also need to add the smarts to include the correct xmlns things
                 at the start of the document. -->
            <cf:long_name>temperature</cf:long_name>
            <cf:units>K</cf:units>
            <wcs:CoverageDescription>
                   ...
            </wcs:CoverageDescription >
            <wcs:CoverageSummary>
                   ...
            </wcs:CoverageSummary >

            <!-- All of the original attributes are here, using the current
                 syntax and a client needs to only grok a finite set of elements
                 to use them with no character content issues we're not currently
                 dealing with  -->
            <Attribute name="long_name" type="String">
                <value>temperature</value>
            </Attribute>
            <Attribute name="units" type="String">
                <value>K</value>
            </Attribute>
            <Attribute name="grid_mapping" type="String">
                <value>crs</value>
            </Attribute>
            <Attribute name="wcs:gridCRS" type="String">
                <value>crs</value>
            </Attribute>
            <Array name="temp">
            ...

### Analysis

[ndp](User:Ndp "wikilink") 16:54, 16 January 2009 (PST)

I think that operationally this is the better of the two proposed
syntaxes.

- We can use XSLT to migrate the foreign XML from the DDX into the RDF
  representation and keep it bound to it's parent DAP object/element.
- It's easy to filter (using XSLT it may more difficult in another API)
  to get the stuff you want.

The following examples are based on what would happen if we followed the
first schema snippet (and beyond in

#### [Example 3](dap:Attribute_Extension_Example_3 "wikilink") Embedded Foreign XML as "global" (top-level) metadata

Where we embed an entire wcs:CoverageDescription and wcs:CoverageSummary
at the top level of a DDX. This most closely reflected by the first
schema snippet above.

#### [Example 3b](dap:Attribute_Extension_Example_3b "wikilink") Embedded Foreign XML as "global" (top-level) metadata

Where we embed an entire wcs:CoverageDescription and wcs:CoverageSummary
at the top level of a DDX. This most closely reflected by the first
schema snippet above.

#### [Example 4](dap:Attribute_Extension_Example_4 "wikilink") Allowing foreign XML to encapsulate and be embedded with the DDX elements

Where we mingle WCS content into the DDX document. A more extreme
version of Example 4 that looks at not just inclusion but encapsulation.

#### [Example 5](dap:Attribute_Extension_Example_5 "wikilink") Allowing foreign XML to encapsulate dap:Attribute values (not so good)

Where we take the "mingling" too far and it breaks.

### Development Plan

How to implement this change:

1.  Adopt this syntax for DAP 3.3
2.  Must be clear about how to organize the stuff in the DDX - make a
    schema or schema snippet that describes where foreign namespace XML
    can occur in the document.

How this will be used by the WCS interface:

1.  Use an AIS to inject foreign namespace XML into the DDX
2.  This content will not be in the DAP attribute namespace
3.  The consumers of this mingled DDX (the OLFS for now) can use various
    XML API's (XSLT, JDOM, etc.) to grab the things they need from the
    DDX.
4.  The OLFS will collect all of the DDX's in the WCS coverage catalog,
    grab their wcs:CoverageSummary and wcs:CoverageDescription elements
    and use them to build responses for GetCapabilities and
    DescribeCoverage requests.
5.  This leaves the OLFS to parse incoming GetCoverage request and to
    build data access URL's (or errors!) based on their content.

## Other Dap Changes

### dap:Attribute

I think we need to rethink the XML representation of dap:Attribute.
Right now we have one XML type, da:Attrbute, which has an xml attribute
named type that controls the "type" of the dap:Attribute. Each value of
"type" implies different semantics, content and possibly different
structure for an instance of dap:Attribute. This reflects the Java (and
possibly C++) implementations of the DAP.

> I think it's a bad thing. [ndp](User:Ndp "wikilink")

> [jimg](User:Jimg "wikilink") 15:23, 17 January 2009 (PST) Why?

> Well, I don't think it's a bad thing that it mimics the java and C++
> implementations. It's a bad thing that they are in fact different
> types, and yet we treat them bundle them as a single type whose
> semantics are a function of the *type* attributes value. My thinking
> is that we should just explicitly represent them as individual types.
> I think that would improve the XML representation of them. I don't
> think it means that the C++ or Java implementations need to be
> re-factored (beyond what it would take to support the different XML
> representation) [ndp](User:Ndp "wikilink") 21:53, 20 January 2009
> (PST)

Whe should consider make each Attribute type it's own "class":

- **<AttByte />** - Holds a Byte valued Attribute.
- **<AttInt16 />** - Holds a 16 bit integer valued Attribute.
- **<AttUInt16 />** - Holds an unsigned 16 bit integer valued Attribute.
- **<AttInt32 />** - Holds a 32 bit integer valued Attribute.
- **<AttUInt32 />** - Holds an unsigned 32 bit integer valued Attribute.
- **<AttFloat32 />** - Holds a 32 bit floating point valued Attribute.
- **<AttFloat64 />** - Holds a 64 bit floating point valued Attribute.
- **<AttString />** - Holds a String valued Attribute.
- **<AttURL />** - Holds a URL valued Attribute.
- **<AttContainer />** - Holds a Container Attribute.

I think this will make for a much better XML representation. One in
which we can more effectively use an XML schema to clearly define the
content model. [jimg](User:Jimg "wikilink") 15:23, 17 January 2009 (PST)
Again, Why?

Of course, the next logical step is probably to dump the concept of:
Attributes all together.


**DAP QUESTION: What is the difference between Attributes and DAP
variables?**

- Is it that Attributes get sent along with the syntactic (structural)
  metadata description? [jimg](User:Jimg "wikilink") 15:22, 17 January
  2009 (PST) Do you mean 'that attribute values get sent along with the
  syntactic metadata? Ans: No, Attributes are labeled as such because
  the person who built the dataset labeled some of the information as
  *variable* and some as *attributes* where the latter describe the
  former. Is the distinction arbitrary? Yes, at some level, but by
  preserving the distinction in DAP we are avoiding loosing it when the
  dataset is viewed by a remote client. BTW, the Attribute values are
  *not* part of the syntactic metadata in the DAS/DDS, on in the DDX.
- Is it that they cannot be constrained? [jimg](User:Jimg "wikilink")
  15:22, 17 January 2009 (PST) It's true that they cannot be constrained
  and that the values are present, but the most important distinction is
  the fidelity of representation; they are attributes in the dataset so
  they are attributes in the DAP representation of the dataset, too.

In the DDX we end up sending all of the Attributes in the DataDDX. **Is
that really what we want?** [jimg](User:Jimg "wikilink") 15:40, 17
January 2009 (PST) Yes, I want each of the responses to be complete. I
think it's a worthwhile concession to make to transport some information
twice.

------------------------------------------------------------------------

> [ndp](User:Ndp "wikilink") 09:18, 21 January 2009 (PST)
> Another way to look at this might be to simply create another
> namespace for Attributes.
>
> For example:
>
> - **<att:Byte />** - Holds a Byte valued Attribute.
> - **<att:Int16 />** - Holds a 16 bit integer valued Attribute.
> - **<att:UInt16 />** - Holds an unsigned 16 bit integer valued
>   Attribute.
> - **<att:Int32 />** - Holds a 32 bit integer valued Attribute.
> - **<att:UInt32 />** - Holds an unsigned 32 bit integer valued
>   Attribute.
> - **<att:Float32 />** - Holds a 32 bit floating point valued
>   Attribute.
> - **<att:Float64 />** - Holds a 64 bit floating point valued
>   Attribute.
> - **<att:String />** - Holds a String valued Attribute.
> - **<att:URL />** - Holds a URL valued Attribute.
> - **<att:Container />** - Holds a Container Attribute.
> - **<att:XML />** - Holds any XML and identifies it as an Attribute.
>   (Assuming we want to wrap foreign XML in an Attribute tag)
>
> This is interesting because the syntactic information is easily
> separable from the semantic using the namespaces and the Attributes
> really become the things that they represent (classes and values)
> [ndp](User:Ndp "wikilink") 09:18, 21 January 2009 (PST)

------------------------------------------------------------------------

### dap:Aliases

Remove the Alias data type from the DAP/DDX the semantics are evil and
we haven't ever made them work.

[DAP3/4](Category:Development "wikilink")
[DAP3/4](Category:DAP4 "wikilink")