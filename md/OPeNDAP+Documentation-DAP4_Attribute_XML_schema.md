### Pending Changes/Updates

This schema needs a few changes/corrections:

1.  The AttributeUrlType Allows the URL type to contain any URI. We need
    to modifiy the schema so that it it is restricted to any URL. Or we
    might wish to relax the DAP definition of the URL Attribute type to
    become a URI.
2.  The AttributeXMLType - Allows any XML not in the DAP Attribute
    namespace. We should try to make this any XML not in the DAP or the
    DAP Attribute namespaces (IE: XML not in
    <http://xml.opendap.org/ns/DAP/3.3/att#> or
    <http://xml.opendap.org/ns/DAP/3.3/dap#>)

<!-- -->

    <?xml version="1.0" encoding="UTF-8"?>
    <xs:schema targetNamespace="http://xml.opendap.org/ns/DAP/3.3/att#"
        xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns:dap="http://xml.opendap.org/ns/DAP/3.3/att#"
        ns="http://xml.opendap.org/ns/DAP/3.3/att#" elementFormDefault="qualified"
        attributeFormDefault="unqualified">
        <!--

        -->
        <xs:element name="Byte" type="AttributeByteType"/>
        <xs:element name="Int16" type="AttributeInt16Type"/>
        <xs:element name="UInt16" type="AttributeUInt16Type"/>
        <xs:element name="Int32" type="AttributeInt32Type"/>
        <xs:element name="UInt32" type="AttributeUInt32Type"/>
        <xs:element name="Float32" type="AttributeFloat32Type"/>
        <xs:element name="Float64" type="AttributeFloat64Type"/>
        <xs:element name="String" type="AttributeStringType"/>
        <xs:element name="Url" type="AttributeUrlType"/>
        <xs:element name="Container" type="AttributeContainerType"/>
        <xs:element name="Xml" type="AttributeXmlType"/>
        <!--

        -->
        <xs:group name="allAttributeTypes">
            <xs:annotation>
                <xs:documentation>Reusable Content Model for Container type</xs:documentation>
            </xs:annotation>
            <xs:choice>
                <xs:element ref="Byte" minOccurs="0" maxOccurs="unbounded"/>
                <xs:element ref="Int16" minOccurs="0" maxOccurs="unbounded"/>
                <xs:element ref="UInt16" minOccurs="0" maxOccurs="unbounded"/>
                <xs:element ref="Int32" minOccurs="0" maxOccurs="unbounded"/>
                <xs:element ref="UInt32" minOccurs="0" maxOccurs="unbounded"/>
                <xs:element ref="Float32" minOccurs="0" maxOccurs="unbounded"/>
                <xs:element ref="Float64" minOccurs="0" maxOccurs="unbounded"/>
                <xs:element ref="String" minOccurs="0" maxOccurs="unbounded"/>
                <xs:element ref="Url" minOccurs="0" maxOccurs="unbounded"/>
                <xs:element ref="Container" minOccurs="0" maxOccurs="unbounded"/>
                <xs:element ref="Xml" minOccurs="0" maxOccurs="unbounded"/>
            </xs:choice>
        </xs:group>
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
        </xs:complexType>
        <!--
           -
           - This allows the URL type to contain anyURI. We need to modifiy the schema so that it really is
           - any URL.
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
        </xs:complexType>
        <!--
           -       - This allows any XML not in the DAP Attribute namespace. We should try to make this
           - any XML not in the DAP or the DAP ATtribute namespace.
           - IE: XML not in http://xml.opendap.org/ns/DAP/3.3/att# or http://xml.opendap.org/ns/DAP/3.3/dap#
           -
        -->
        <xs:complexType name="AttributeXmlType">
            <xs:annotation>
                <xs:documentation>DAS Attribute XML Type</xs:documentation>
            </xs:annotation>
            <xs:choice>
                <xs:any namespace="##other" minOccurs="1" maxOccurs="unbounded" processContents="strict"
                />
            </xs:choice>
            <xs:attribute name="name" type="xs:string" use="required"/>
        </xs:complexType>
        <!--

        -->
    </xs:schema>