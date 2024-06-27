## Add the concept of "relationship" to the DAP Attribute model

### Goal

Provide a mechanism for explicitly describing the relationship of a DAP
Attribute object to its parent DAP object (Be it a DAP Variable or
Attribute).

### Background

Until now the DAP Attribute model has held the (not formally encoded)
assumption that all DAP Attribute objects "belong" to the DAP Variable
or DAP Attribute that contains them: "The variable *x* has an Attribute
called *foo*." This is what we will call a ***has-a*** relationship.


***has-a*** expresses that the relationship its parent DAP object has
towards it as "Has a". As in: My parent has the propert(y \| ies) that I
represent.

With the introduction of more complex metadata into the DAP the
possibility for another relationship now exists: ***is-a***


***is-a*** expresses that the relationship its parent DAP object has
towards it as "Is a". As in: My parent is another representation of the
thing that I represent.

The discussion here: <https://cf-pcmdi.llnl.gov/trac/ticket/27> is
relevant.

### Design

We will add a new, optional XML attribute named ***relationship*** to
the Attribute element in the DAP 2 DDX. It may have the value of either
"has-a" or "is-a", if omitted it's value defaults to "has-a".

#### Schema Snippet

`  `<xs:complexType name="DASAttribute">
`       `<xs:annotation>
`           `<xs:documentation>`DAS Attribute Type`</xs:documentation>
`       `</xs:annotation>
`       `<xs:choice>
`           `<xs:sequence>
`               `<xs:element name="value" type="xs:string" maxOccurs="unbounded"/>
`           `</xs:sequence>
`           `<xs:sequence>
`               `<xs:element ref="Attribute" minOccurs="0" maxOccurs="unbounded"/>
`               `<xs:element ref="Alias" minOccurs="0" maxOccurs="unbounded"/>
`           `</xs:sequence>
`       `</xs:choice>
`       `<xs:attribute name="name" type="xs:string" use="required"/>
`       `<xs:attribute name="type" type="dap2:DataType" use="required"/>
`       `**<xs:attribute name="relationship" type="relationships" use="optional" default="has-a"/>**
`   `</xs:complexType>

`   `
`   `**<xs:simpleType name="relationships">**
`       `**<xs:restriction base="xs:string">**
`           `**<xs:enumeration value="is-a"/>**
`           `**<xs:enumeration value="has-a"/>**
`       `**</xs:restriction>**
`   `**</xs:simpleType>**

## Add a DAP Attribute with type *OtherXML*.

### Goal

Provide a mechanism for including any XML markup from namespaces other
than the DAP namespaces as metadata associated with any DAP variable.

### Background

In order to create a more flexible metadata channel for the DAP we will
add the new type "OtherXML" to the Attribute objects to the DAP
Attribute data model.

The *OtherXML* element is allowed to contain any XML from a non DAP
namespace. For example, XML content from the GML or WCS namespaces could
be included here. Even an Open Office document describing the content
would be legal.

### Design

Add an new Attribute type, ***OtherXML*** to the DAP Attribute content
model. The type is easy add to the DAP 2 schema, but it is not possible
within the current DDX design to have the schema capture all of its
semantics.:

1.  This new type of Attribute does not need (and probably should be
    barred from having) a *name* attribute.
2.  The use of the *relationship* attribute for dap:Attribute objects of
    type OtherXML has subtly different semantics. They apply to the
    content of the Attribute, but not the Attribute itself.
    - A value of ***has-a*** expresses the relationship of the Attribute
      object's parent DAP object has towards the Attribute objects
      content as "Has A". As in: My parent has the propert(y \| ies)
      that I contain.
    - A value of ***is-a*** expresses that the relationship of the
      Attribute object's parent DAP object has towards the Attribute
      objects content as "Is A". As in: My parent can be seen as having
      the same semantics as the thing(s) that I contain.

The OtherXML type is essentially a nameless wrapper that defines the
relationship between it's parent container and it's contents. In a sense
the OtherXML Attribute type is invisible (thus the reasoning behind it
being nameless).

Both of these issues can be addressed in the proposed changes to the XML
representation of Attributes below.

(*Since this change is taking place in conjunction with re-factoring the
XML representation of DAP Attribute objects this new Attribute type will
be included those sections.*)

#### Schema

In the DAP2 DDX schema this change would be made by adding a new value
to the DataType type enumeration used by the dap:Attribute element's
*type* attribute:

`   `<xs:simpleType name="DataType">
`       `<xs:restriction base="xs:string">
`           `<xs:enumeration value="Byte"/>
`           `<xs:enumeration value="Int16"/>
`           `<xs:enumeration value="UInt16"/>
`           `<xs:enumeration value="Int32"/>
`           `<xs:enumeration value="UInt32"/>
`           `<xs:enumeration value="Float32"/>
`           `<xs:enumeration value="Float64"/>
`           `<xs:enumeration value="String"/>
`           `<xs:enumeration value="URL"/>
`           `<xs:enumeration value="Container"/>
`           `**<xs:enumeration value="OtherXML"/>**
`       `</xs:restriction>
`   `</xs:simpleType>

However, in the DAP2 schema no content rules are available for the
Attribute elements. This makes it easy to add the new type, but doesn't
provide any functional information about it's content.

### Example Document

        <?xml version="1.0" encoding="UTF-8"?>
        <Dataset name="200803061600_HFRadar_USEGC_6km_rtv_SIO.nc"
        ns="http://xml.opendap.org/ns/DAP/3.3#"
        xmlns:dap="http://xml.opendap.org/ns/DAP/3.3#"
        dap_version="3.3"
        xmlns:xml="http://www.w3.org/XML/1998/namespace"
        xml:base="http://localhost:8080/opendap/coverage/200803061600_HFRadar_USEGC_6km_rtv_SIO.nc.ddx"


            <Attribute name="wcsStuff" type="OtherXML" relationship="is-a">
                <CoverageDescription ns="http://www.opengis.net/wcs/1.1" xmlns:ows="http://www.opengis.net/ows/1.1"
                                     xmlns:owcs="http://www.opengis.net/wcs/1.1/ows"
                                     xmlns:gml="http://www.opengis.net/gml/3.2" xmlns:xlink="http://www.w3.org/1999/xlink"
                                     >
                    <ows:Title>Near-Real Time Surface Ocean Velocity</ows:Title>
                    <ows:Abstract>CoverageDescription generated by OPeNDAP WCS UseCase 2.0</ows:Abstract>
                    <Identifier>coverage/200803061600_HFRadar_USEGC_6km_rtv_SIO.nc</Identifier>
                    <Domain>
                        <SpatialDomain>
                            <ows:BoundingBox crs="urn:ogc:def:crs:EPSG::4326">
                                <ows:LowerCorner>-97.8839 21.736</ows:LowerCorner>
                                <ows:UpperCorner>-57.2312 46.4944</ows:UpperCorner>
                            </ows:BoundingBox>
                        </SpatialDomain>
                        <TemporalDomain>
                            <gml:timePosition>2008-03-27T16:00:00.000Z</gml:timePosition>
                        </TemporalDomain>
                    </Domain>
                    <Range>
                    </Range>
                    <SupportedCRS>urn:ogc:def:crs:EPSG::4326</SupportedCRS>
                    <SupportedFormat>netcdf-cf1.0</SupportedFormat>
                    <SupportedFormat>dap2.0</SupportedFormat>
                </CoverageDescription>
            </Attribute>

            <Grid name="u">
                <Attribute name="one" type="OtherXML" relationship="has-a">
                    <units ns="http://my.foo.com/ns/units#" />
                </Attribute>
                <Attribute name="two" type="OtherXML" relationship="is-a">
                    <Field ns="http://www.opengis.net/wcs/1.1" xmlns:ows="http://www.opengis.net/ows/1.1"
                                     xmlns:owcs="http://www.opengis.net/wcs/1.1/ows"
                                     xmlns:gml="http://www.opengis.net/gml/3.2" xmlns:xlink="http://www.w3.org/1999/xlink"
                                     >
                        <ows:Title>surface_eastward_sea_water_velocity</ows:Title>
                        <ows:Abstract>Eastward component of a 2D sea surface velocity vector.</ows:Abstract>
                        <Identifier>u</Identifier>
                        <Definition>
                            <ows:AnyValue/>
                        </Definition>
                        <NullValue>-32768</NullValue>
                        <owcs:InterpolationMethods>
                            <owcs:DefaultMethod>nearest</owcs:DefaultMethod>
                        </owcs:InterpolationMethods>
                    </Field>
                </Attribute>
                <Array name="u">
                    <Int16/>
                    <dimension name="time" size="1"/>
                    <dimension name="lat" size="460"/>
                    <dimension name="lon" size="701"/>
                </Array>
                <Map name="time">
                    <Int32/>
                    <dimension name="time" size="1"/>
                </Map>
                <Map name="lat">
                    <Float32/>
                    <dimension name="lat" size="460"/>
                </Map>
                <Map name="lon">
                    <Float32/>
                    <dimension name="lon" size="701"/>
                </Map>
            </Grid>

        </Dataset>

------------------------------------------------------------------------

## Re-factoring the representation of DAP Attribute objects.

### Goals

Provide an improved XML representation of the DAP Attributes.

### Background

The original DDX represented Attribute objects as a single XML element
dap:Attribute. The type of the DAP Attribute was represented as the
value of an XML attribute names **type**. For example:

`   <Attribute name="bears" `**`type="Container"`**`>`
`       <Attribute name="act" `**`type="String"`**`>`
`           `<value>`text string\\012\\011123`</value>
`       `</Attribute>
`       <Attribute name="acs" `**`type="Int16"`**`>`
`           `<value>`-40`</value>
`       `</Attribute>
`   `</Attribute>

This is unlike the scheme for variables in which each one is represented
by an XML element whose name reflects the type of the variable:

`       `<String name="satellite"/>
`       `<Int32 name="year"/>

A more parallel representation can be achieved by creating a separate
namespace for the DAP Attribute data model. This way the two
representations can more closely resemble each other, and they can be
easily filtered and manipulated using namespace aware processing (which
really is a key component of the XML use model).

Additionally, the way that our current Attribute semantics cannot be
expressed in XML Schema 1.0, as they require "conditional type
assignment" which is only available in (the as yet to released) XML
Schema 1.1

### Design

There are two design candidates. One in which we use separate namespaces
for DAP variables and DAP Attribute. The other simply provides a second
set of specific Attribute types separate from the DAP variable types.

------------------------------------------------------------------------

#### Candidate 1: Attributes In A Separate Namespace

Each type of DAP Attribute type will have its own XML element
representation in the DAP Attribute namespace:
**http://xml.opendap.org/ns/DAP/3.3/att#**

- ***xmlns:att="<http://xml.opendap.org/ns/DAP/3.3/att#>"*** Using the
  att prefix to identify the DAP Attribute namespace.
- **<att:Byte />** - Represents a byte valued Attribute.
- **<att:Int16 />** - Represents a 16 bit integer valued Attribute.
- **<att:UInt16 />** - Represents an unsigned 16 bit integer valued
  Attribute.
- **<att:Int32 />** - Represents a 32 bit integer valued Attribute.
- **<att:UInt32 />** - Represents an unsigned 32 bit integer valued
  Attribute.
- **<att:Float32 />** - Represents a 32 bit floating point valued
  Attribute.
- **<att:Float64 />** - Represents a 64 bit floating point valued
  Attribute.
- **<att:String />** - Represents a String valued Attribute.
- **<att:URL />** - Represents a URL valued Attribute.
- **<att:Container />** - Represents a Container Attribute.
- **<att:XML />** - Represents an Attribute object that may hold any XML
  not in the DAP namespaces and identifies it as an Attribute. (Assuming
  we want to wrap foreign XML in an Attribute tag)

##### Schema

Here is the **[prototype schema for the DAP4
namespace](DAP4_XML_schema "wikilink")**

- This schema document is made much simpler due to moving the DAP
  Attributes to their own schema.

Here is the **[prototype schema for the DAP4 Attribute
namespace](DAP4_Attribute_XML_schema "wikilink")** This schema needs a
few changes/corrections:

1.  The AttributeUrlType Allows the URL type to contain any URI. We need
    to modifiy the schema so that it it is restricted to any URL. Or we
    might wish to relax the DAP definition of the URL Attribute type to
    become a URI.
2.  The AttributeXMLType - Allows any XML not in the DAP Attribute
    namespace. We should try to make this any XML not in the DAP or the
    DAP Attribute namespaces (IE: XML not in
    <http://xml.opendap.org/ns/DAP/3.3/att#> or
    <http://xml.opendap.org/ns/DAP/3.3/dap#>)

------------------------------------------------------------------------

#### Candidate 2: Separate Attribute Types In The Same Namespace

Each type of DAP Attribute type will have its own XML element
representation in the DAP Attribute namespace:
**http://xml.opendap.org/ns/DAP/3.3/att#**

- ***xmlns:att="<http://xml.opendap.org/ns/DAP/3.5/dap#>"***
- **<aByte />** - Represents a byte valued Attribute.
- **<aInt16 />** - Represents a 16 bit integer valued Attribute.
- **<aUInt16 />** - Represents an unsigned 16 bit integer valued
  Attribute.
- **<aInt32 />** - Represents a 32 bit integer valued Attribute.
- **<aUInt32 />** - Represents an unsigned 32 bit integer valued
  Attribute.
- **<aFloat32 />** - Represents a 32 bit floating point valued
  Attribute.
- **<aFloat64 />** - Represents a 64 bit floating point valued
  Attribute.
- **<aString />** - Represents a String valued Attribute.
- **<aURL />** - Represents a URL valued Attribute.
- **<AttContainer />** - Represents a Container Attribute.
- **<OtherXML />** - Represents an Attribute object that may hold any
  XML not in the DAP namespaces and identifies it as an Attribute.
  - If this element has a *relationship* attribute equal to "is-a", that
    indicates that its contents are in essence an alternate semantic
    rpresentation of the Attribute's parent DAP object.
  - If this element has a *relationship* attribute equal to "has-a",
    that indicates that its contents are in to the Attribute's parent
    DAP object as a property.

##### DAP4 Schema Prototype

*DAP4 Variables Schema*

<?xml version="1.0" encoding="UTF-8"?>

`   `<xs:schema targetNamespace="http://xml.opendap.org/ns/DAP/3.5/dap#"
        xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns:dap="http://xml.opendap.org/ns/DAP/3.5/dap#"
        ns="http://xml.opendap.org/ns/DAP/3.5/dap#" elementFormDefault="qualified"
        attributeFormDefault="unqualified">
`   `
`   `
`       `
`   `
`       `<xs:include schemaLocation="dapAtt_3.5.xsd"/>
`   `
`       `
`       `<xs:element name="Dataset" type="DAP_Dataset"/>
`       `<xs:element name="Map" type="Array"/>
`       `<xs:element name="Byte" type="BaseType"/>
`       `<xs:element name="Int16" type="BaseType"/>
`       `<xs:element name="UInt16" type="BaseType"/>
`       `<xs:element name="Int32" type="BaseType"/>
`       `<xs:element name="UInt32" type="BaseType"/>
`       `<xs:element name="Float32" type="BaseType"/>
`       `<xs:element name="Float64" type="BaseType"/>
`       `<xs:element name="String" type="BaseType"/>
`       `<xs:element name="Url" type="BaseType"/>
`       `<xs:element name="Array" type="Array"/>
`       `<xs:element name="Grid" type="Grid"/>
`       `<xs:element name="Structure" type="Structure"/>
`       `<xs:element name="Sequence" type="Sequence"/>
`       `
`   `
`       `
`       `<xs:group name="allDapTypes">
`           `<xs:annotation>
`               `<xs:documentation>`Reusable Content Model for Complex DODS types`</xs:documentation>
`           `</xs:annotation>
`           `<xs:choice>
`               `<xs:element ref="Byte" minOccurs="0" maxOccurs="unbounded"/>
`               `<xs:element ref="Int16" minOccurs="0" maxOccurs="unbounded"/>
`               `<xs:element ref="UInt16" minOccurs="0" maxOccurs="unbounded"/>
`               `<xs:element ref="Int32" minOccurs="0" maxOccurs="unbounded"/>
`               `<xs:element ref="UInt32" minOccurs="0" maxOccurs="unbounded"/>
`               `<xs:element ref="Float32" minOccurs="0" maxOccurs="unbounded"/>
`               `<xs:element ref="Float64" minOccurs="0" maxOccurs="unbounded"/>
`               `<xs:element ref="String" minOccurs="0" maxOccurs="unbounded"/>
`               `<xs:element ref="Url" minOccurs="0" maxOccurs="unbounded"/>
`               `<xs:element ref="Array" minOccurs="0" maxOccurs="unbounded"/>
`               `<xs:element ref="Grid" minOccurs="0" maxOccurs="unbounded"/>
`               `<xs:element ref="Structure" minOccurs="0" maxOccurs="unbounded"/>
`               `<xs:element ref="Sequence" minOccurs="0" maxOccurs="unbounded"/>
`           `</xs:choice>
`       `</xs:group>
`       `
`       `<xs:complexType name="DAP_Dataset">
`           `<xs:annotation>
`               `<xs:documentation>`This is the XML representation of a DODS DDS`
`                   object.`</xs:documentation>
`           `</xs:annotation>
`           `<xs:sequence>
`               `<xs:group ref="allAttributeTypes" minOccurs="0" maxOccurs="unbounded"/>
`               `<xs:group ref="allDapTypes" minOccurs="0" maxOccurs="unbounded"/>
`           `</xs:sequence>
`           `<xs:attribute name="name" type="xs:string" use="required"/>
`       `</xs:complexType>
`   `
`       `
`       `<xs:complexType name="BaseType">
`           `<xs:annotation>
`               `<xs:documentation>`DODS Base Type`</xs:documentation>
`           `</xs:annotation>
`           `<xs:sequence>
`               `<xs:group ref="allAttributeTypes" minOccurs="0" maxOccurs="unbounded"/>
`           `</xs:sequence>
`           `<xs:attribute name="name" type="xs:string"/>
`       `</xs:complexType>
`       `
`       `<xs:complexType name="Array">
`           `<xs:complexContent>
`               `<xs:extension base="BaseType">
`                   `<xs:sequence>
`                       `<xs:choice minOccurs="1" maxOccurs="1">
`                           `<xs:element ref="Byte"/>
`                           `<xs:element ref="Int16"/>
`                           `<xs:element ref="UInt16"/>
`                           `<xs:element ref="Int32"/>
`                           `<xs:element ref="UInt32"/>
`                           `<xs:element ref="Float32"/>
`                           `<xs:element ref="Float64"/>
`                           `<xs:element ref="String"/>
`                           `<xs:element ref="Url"/>
`                           `<xs:element ref="Grid"/>
`                           `<xs:element ref="Structure"/>
`                           `<xs:element ref="Sequence"/>
`                       `</xs:choice>
`                       `<xs:element name="dimension" type="dap:ArrayDimension" minOccurs="1"
                            maxOccurs="unbounded"/>
`                   `</xs:sequence>
`               `</xs:extension>
`           `</xs:complexContent>
`       `</xs:complexType>
`       `
`       `<xs:complexType name="ArrayDimension">
`           `<xs:attribute name="name" type="xs:string"/>
`           `<xs:attribute name="size" type="xs:integer" use="required"/>
`       `</xs:complexType>
`       `
`       `<xs:complexType name="Grid">
`           `<xs:complexContent>
`               `<xs:extension base="dap:BaseType">
`                   `<xs:sequence>
`                       `<xs:element ref="Array" minOccurs="1" maxOccurs="1"/>
`                       `<xs:element ref="Map" minOccurs="1" maxOccurs="unbounded"/>
`                   `</xs:sequence>
`               `</xs:extension>
`           `</xs:complexContent>
`       `</xs:complexType>
`       `
`       `<xs:complexType name="Structure">
`           `<xs:complexContent>
`               `<xs:extension base="BaseType">
`                   `<xs:group ref="allDapTypes" minOccurs="1" maxOccurs="unbounded"/>
`               `</xs:extension>
`           `</xs:complexContent>
`       `</xs:complexType>
`       `
`       `<xs:complexType name="Sequence">
`           `<xs:complexContent>
`               `<xs:extension base="BaseType">
`                   `<xs:group ref="allDapTypes" minOccurs="1" maxOccurs="unbounded"/>
`               `</xs:extension>
`           `</xs:complexContent>
`       `</xs:complexType>
`   `
`       `
`   `</xs:schema>

*DAP4 Attributes Schema*

    <?xml version="1.0" encoding="UTF-8"?>
    <xs:schema targetNamespace="http://xml.opendap.org/ns/DAP/3.5/dap#"
        xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns:dap="http://xml.opendap.org/ns/DAP/3.5/dap#"
        ns="http://xml.opendap.org/ns/DAP/3.5/dap#" elementFormDefault="qualified"
        attributeFormDefault="unqualified">
        <!--

        -->
        <xs:element name="aByte" type="AttributeByteType"/>
        <xs:element name="aInt16" type="AttributeInt16Type"/>
        <xs:element name="aUInt16" type="AttributeUInt16Type"/>
        <xs:element name="aInt32" type="AttributeInt32Type"/>
        <xs:element name="aUInt32" type="AttributeUInt32Type"/>
        <xs:element name="aFloat32" type="AttributeFloat32Type"/>
        <xs:element name="aFloat64" type="AttributeFloat64Type"/>
        <xs:element name="aString" type="AttributeStringType"/>
        <xs:element name="aUrl" type="AttributeUrlType"/>
        <xs:element name="AttContainer" type="AttributeContainerType"/>
        <xs:element name="OtherXML" type="AttributeXmlType"/>
        <!--

        -->
        <xs:group name="allAttributeTypes">
            <xs:annotation>
                <xs:documentation>Reusable Content Model for Containers of Attribute
                    types</xs:documentation>
            </xs:annotation>
            <xs:choice>
                <xs:element ref="aByte" minOccurs="0" maxOccurs="unbounded"/>
                <xs:element ref="aInt16" minOccurs="0" maxOccurs="unbounded"/>
                <xs:element ref="aUInt16" minOccurs="0" maxOccurs="unbounded"/>
                <xs:element ref="aInt32" minOccurs="0" maxOccurs="unbounded"/>
                <xs:element ref="aUInt32" minOccurs="0" maxOccurs="unbounded"/>
                <xs:element ref="aFloat32" minOccurs="0" maxOccurs="unbounded"/>
                <xs:element ref="aFloat64" minOccurs="0" maxOccurs="unbounded"/>
                <xs:element ref="aString" minOccurs="0" maxOccurs="unbounded"/>
                <xs:element ref="aUrl" minOccurs="0" maxOccurs="unbounded"/>
                <xs:element ref="AttContainer" minOccurs="0" maxOccurs="unbounded"/>
                <xs:element ref="OtherXML" minOccurs="0" maxOccurs="unbounded"/>
            </xs:choice>
        </xs:group>
        <!--

        -->
        <xs:simpleType name="relationships">
            <xs:restriction base="xs:string">
                <xs:enumeration value="is-a"/>
                <xs:enumeration value="has-a"/>
            </xs:restriction>
        </xs:simpleType>
        <!--

        -->
        <xs:complexType name="AttributeContainerType">
            <xs:annotation>
                <xs:documentation>DAP Attribute Container Type</xs:documentation>
            </xs:annotation>
            <xs:choice>
                <xs:group ref="allAttributeTypes" minOccurs="1" maxOccurs="unbounded"/>
            </xs:choice>
            <xs:attribute name="name" type="xs:string" use="required"/>
            <xs:attribute name="relationship" type="relationships" use="optional" default="has-a"/>
        </xs:complexType>
        <!--

         -->
        <xs:complexType name="AttributeByteType">
            <xs:annotation>
                <xs:documentation>DAP Attribute Byte Type</xs:documentation>
            </xs:annotation>
            <xs:choice>
                <xs:element name="value" type="xs:unsignedByte" minOccurs="1" maxOccurs="unbounded"/>
            </xs:choice>
            <xs:attribute name="name" type="xs:string" use="required"/>
            <xs:attribute name="relationship" type="relationships" use="optional" default="has-a"/>
        </xs:complexType>
        <!--

        -->
        <xs:complexType name="AttributeInt16Type">
            <xs:annotation>
                <xs:documentation>DAP Attribute Int16 Type</xs:documentation>
            </xs:annotation>
            <xs:choice>
                <xs:element name="value" type="xs:short" minOccurs="1" maxOccurs="unbounded"/>
            </xs:choice>
            <xs:attribute name="name" type="xs:string" use="required"/>
            <xs:attribute name="relationship" type="relationships" use="optional" default="has-a"/>
        </xs:complexType>
        <!--



        -->
        <xs:complexType name="AttributeUInt16Type">
            <xs:annotation>
                <xs:documentation>DAP Attribute UInt16 Type</xs:documentation>
            </xs:annotation>
            <xs:choice>
                <xs:element name="value" type="xs:unsignedShort" minOccurs="1" maxOccurs="unbounded"/>
            </xs:choice>
            <xs:attribute name="name" type="xs:string" use="required"/>
            <xs:attribute name="relationship" type="relationships" use="optional" default="has-a"/>
        </xs:complexType>
        <!--

        -->
        <xs:complexType name="AttributeInt32Type">
            <xs:annotation>
                <xs:documentation>DAP Attribute Int32 Type</xs:documentation>
            </xs:annotation>
            <xs:choice>
                <xs:element name="value" type="xs:int" minOccurs="1" maxOccurs="unbounded"/>
            </xs:choice>
            <xs:attribute name="name" type="xs:string" use="required"/>
            <xs:attribute name="relationship" type="relationships" use="optional" default="has-a"/>
        </xs:complexType>
        <!--

         -->
        <xs:complexType name="AttributeUInt32Type">
            <xs:annotation>
                <xs:documentation>DAP Attribute UInt32 Type</xs:documentation>
            </xs:annotation>
            <xs:choice>
                <xs:element name="value" type="xs:unsignedInt" minOccurs="1" maxOccurs="unbounded"/>
            </xs:choice>
            <xs:attribute name="name" type="xs:string" use="required"/>
            <xs:attribute name="relationship" type="relationships" use="optional" default="has-a"/>
        </xs:complexType>
        <!--

         -->
        <xs:complexType name="AttributeFloat32Type">
            <xs:annotation>
                <xs:documentation>DAP Attribute Float32 Type</xs:documentation>
            </xs:annotation>
            <xs:choice>
                <xs:element name="value" type="xs:float" minOccurs="1" maxOccurs="unbounded"/>
            </xs:choice>
            <xs:attribute name="name" type="xs:string" use="required"/>
            <xs:attribute name="relationship" type="relationships" use="optional" default="has-a"/>
        </xs:complexType>
        <!--

        -->
        <xs:complexType name="AttributeFloat64Type">
            <xs:annotation>
                <xs:documentation>DAP Attribute Float64 Type</xs:documentation>
            </xs:annotation>
            <xs:choice>
                <xs:element name="value" type="xs:double" minOccurs="1" maxOccurs="unbounded"/>
            </xs:choice>
            <xs:attribute name="name" type="xs:string" use="required"/>
            <xs:attribute name="relationship" type="relationships" use="optional" default="has-a"/>
        </xs:complexType>
        <!--

         -->
        <xs:complexType name="AttributeStringType">
            <xs:annotation>
                <xs:documentation>DAS Attribute String Type</xs:documentation>
            </xs:annotation>
            <xs:choice>
                <xs:element name="value" type="xs:string" minOccurs="1" maxOccurs="unbounded"/>
            </xs:choice>
            <xs:attribute name="name" type="xs:string" use="required"/>
            <xs:attribute name="relationship" type="relationships" use="optional" default="has-a"/>
        </xs:complexType>
        <!--
           -
           -
           -
           -
           - This allows the URL type to contain anyURI. Can we modifiy the schema so that it is
           - resricted to just URLs? (Probably not based on aerdin the W3C stuff on URI/URL)
           -
           - OR
           -
           - We need to relax the DAP definition of the URL Attribute type.
           -
        -->
        <xs:complexType name="AttributeUrlType">
            <xs:annotation>
                <xs:documentation>DAS Attribute URL Type</xs:documentation>
            </xs:annotation>
            <xs:choice>
                <xs:element name="value" type="xs:anyURI" minOccurs="1" maxOccurs="unbounded"/>
            </xs:choice>
            <xs:attribute name="name" type="xs:string" use="required"/>
            <xs:attribute name="relationship" type="relationships" use="optional" default="has-a"/>
        </xs:complexType>
        <!--
           -
           -
           -
           -
           - This allows any XML not in the DAP Attribute namespace.
           - The ##other only applies to the immediate
           - children. So the childrens children could still
           - contain elements from our namespace.
           -
        -->
        <xs:complexType name="AttributeXmlType">
            <xs:annotation>
                <xs:documentation>DAS Attribute XML Type</xs:documentation>
            </xs:annotation>
            <xs:choice>
                <xs:any namespace="##other" minOccurs="1" maxOccurs="unbounded" processContents="skip"/>
            </xs:choice>
            <xs:attribute name="relationship" type="relationships" use="optional" default="has-a"/>
        </xs:complexType>


    </xs:schema>

### Example Document

    <?xml version="1.0" encoding="UTF-8"?>
    <Dataset name="200803061600_HFRadar_USEGC_6km_rtv_SIO.nc"
             ns="http://xml.opendap.org/ns/DAP/3.5#"
             xmlns:dap="http://xml.opendap.org/ns/DAP/3.5#"
             dap_version="3.3"
             xmlns:xml="http://www.w3.org/XML/1998/namespace"
             xml:base="http://localhost:8080/opendap/coverage/200803061600_HFRadar_USEGC_6km_rtv_SIO.nc.ddx">


        <OtherXML relationship="is-a">
            <CoverageDescription ns="http://www.opengis.net/wcs/1.1" xmlns:ows="http://www.opengis.net/ows/1.1"
                                 xmlns:owcs="http://www.opengis.net/wcs/1.1/ows" xmlns:gml="http://www.opengis.net/gml/3.2"
                                 xmlns:xlink="http://www.w3.org/1999/xlink"
                    >
                <ows:Title>Near-Real Time Surface Ocean Velocity</ows:Title>
                <ows:Abstract>CoverageDescription generated by OPeNDAP WCS UseCase 2.0</ows:Abstract>
                <Identifier>coverage/200803061600_HFRadar_USEGC_6km_rtv_SIO.nc</Identifier>
                <Domain>
                    <SpatialDomain>
                        <ows:BoundingBox crs="urn:ogc:def:crs:EPSG::4326">
                            <ows:LowerCorner>-97.8839 21.736</ows:LowerCorner>
                            <ows:UpperCorner>-57.2312 46.4944</ows:UpperCorner>
                        </ows:BoundingBox>
                    </SpatialDomain>
                    <TemporalDomain>
                        <gml:timePosition>2008-03-27T16:00:00.000Z</gml:timePosition>
                    </TemporalDomain>
                </Domain>
                <Range>
                </Range>
                <SupportedCRS>urn:ogc:def:crs:EPSG::4326</SupportedCRS>
                <SupportedFormat>netcdf-cf1.0</SupportedFormat>
                <SupportedFormat>dap2.0</SupportedFormat>
            </CoverageDescription>

        </OtherXML>

        <AttContainer name="NC_GLOBAL" >
            <aString name="title" >
                <avalue>Near-Real Time Surface Ocean Velocity</avalue>
            </aString>
            <aString name="institution">
                <avalue>Scripps Institution of Oceanography</avalue>
            </aString>
            <aString name="source">
                <avalue>Surface Ocean HF-Radar</avalue>
            </aString>
            <aString name="history" >
                <avalue>12-Mar-2008 22:26:19: NetCDF file created</avalue>
            </aString>
            <aString name="references">
                <avalue>Terrill, E. et al., 2006. Data Management and Real-time\\012Distribution in the HF-Radar National
                    Network. Proceedings\\012of the MTS/IEEE Oceans 2006 Conference, Boston MA,\\012September 2006.
                </avalue>
            </aString>
        </AttContainer>

        <Grid name="u">
            <OtherXML relationship="has-a">
                <units ns="http://www.opengis.net/WxS />
            </OtherXML >
            < OtherXML relationship="is-a" >
                <Field ns="http://www.opengis.net/wcs/1.1" xmlns:ows="http://www.opengis.net/ows/1.1"
                                 xmlns:owcs="http://www.opengis.net/wcs/1.1/ows" xmlns:gml="http://www.opengis.net/gml/3.2"
                                 xmlns:xlink="http://www.w3.org/1999/xlink"
                    >
                    <ows:Title>surface_eastward_sea_water_velocity</ows:Title>
                    <ows:Abstract>Eastward component of a 2D sea surface velocity vector.</ows:Abstract>
                    <Identifier>u</Identifier>
                    <Definition>
                        <ows:AnyValue/>
                    </Definition>
                    <NullValue>-32768</NullValue>
                    <owcs:InterpolationMethods>
                        <owcs:DefaultMethod>nearest</owcs:DefaultMethod>
                    </owcs:InterpolationMethods>
                </Field>
            </OtherXML >
            <aString name="standard_name" >
                <value>surface_eastward_sea_water_velocity</value>
            </aString>
            <aString name="units" >
                <value>m s-1</value>
            </aString>
            <aInt16 name="_FillValue" >
                <value>-32768</value>
            </aInt16>
            <aFloat32 name="scale_factor" >
                <value>0.009999999776</value>
            </aFloat32>
            <aString name="ancillary_variables">
                <value>DOPx</value>
            </aString>
            <Array name="u">
                <Int16/>
                <dimension name="time" size="1"/>
                <dimension name="lat" size="460"/>
                <dimension name="lon" size="701"/>
            </Array>
            <Map name="time">
                <Int32/>
                <dimension name="time" size="1"/>
            </Map>
            <Map name="lat">
                <Float32/>
                <dimension name="lat" size="460"/>
            </Map>
            <Map name="lon">
                <Float32/>
                <dimension name="lon" size="701"/>
            </Map>
        </Grid>

    </Dataset>

[DAP3/4](Category:Development "wikilink")
[DAP3/4](Category:DAP4 "wikilink")