    <?xml version="1.0" encoding="UTF-8"?>
    <!-- edited with XML Spy v4.4 U (http://www.xmlspy.com) by Nathan Potter (OSU-COAS) -->
    <xs:schema targetNamespace="http://xml.opendap.org/ns/DAP/3.3/dap#"
        xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns:dap="http://xml.opendap.org/ns/DAP/3.3/dap#"
        xmlns:att="http://xml.opendap.org/ns/DAP/3.3/att#"
        ns="http://xml.opendap.org/ns/DAP/3.3/dap#" elementFormDefault="qualified"
        attributeFormDefault="unqualified">


        <!-- ==============================================================
            includes and imports
            ============================================================== -->

        <xs:import namespace="http://xml.opendap.org/ns/DAP/3.3/att#" schemaLocation="dapAtt_3.x.xsd"/>

        <!--

        -->
        <xs:element name="Dataset" type="DODS_Dataset"/>
        <xs:element name="Map" type="Array"/>
        <xs:element name="Byte" type="BaseType"/>
        <xs:element name="Int16" type="BaseType"/>
        <xs:element name="UInt16" type="BaseType"/>
        <xs:element name="Int32" type="BaseType"/>
        <xs:element name="UInt32" type="BaseType"/>
        <xs:element name="Float32" type="BaseType"/>
        <xs:element name="Float64" type="BaseType"/>
        <xs:element name="String" type="BaseType"/>
        <xs:element name="Url" type="BaseType"/>
        <xs:element name="Array" type="Array"/>
        <xs:element name="Grid" type="Grid"/>
        <xs:element name="Structure" type="Structure"/>
        <xs:element name="Sequence" type="Sequence"/>
        <!--

        -->
        <xs:group name="allDapTypes">
            <xs:annotation>
                <xs:documentation>Reusable Content Model for Complex DODS types</xs:documentation>
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
                <xs:element ref="Array" minOccurs="0" maxOccurs="unbounded"/>
                <xs:element ref="Grid" minOccurs="0" maxOccurs="unbounded"/>
                <xs:element ref="Structure" minOccurs="0" maxOccurs="unbounded"/>
                <xs:element ref="Sequence" minOccurs="0" maxOccurs="unbounded"/>
            </xs:choice>
        </xs:group>
        <!--

        -->
        <xs:complexType name="DODS_Dataset">
            <xs:annotation>
                <xs:documentation>This is the XML representation of a DODS DDS
                    object.</xs:documentation>
            </xs:annotation>
            <xs:sequence>
                <xs:group ref="att:allAttributeTypes" minOccurs="0" maxOccurs="unbounded"/>
                <xs:group ref="allDapTypes" minOccurs="0" maxOccurs="unbounded"/>
            </xs:sequence>
            <xs:attribute name="name" type="xs:string" use="required"/>
        </xs:complexType>
        <!--

        -->
        <xs:complexType name="BaseType">
            <xs:annotation>
                <xs:documentation>DODS Base Type</xs:documentation>
            </xs:annotation>
            <xs:sequence>
                <xs:group ref="att:allAttributeTypes" minOccurs="0" maxOccurs="unbounded"/>
            </xs:sequence>
            <xs:attribute name="name" type="xs:string"/>
        </xs:complexType>
        <!--

        -->
        <xs:complexType name="Array">
            <xs:complexContent>
                <xs:extension base="BaseType">
                    <xs:sequence>
                        <xs:choice minOccurs="1" maxOccurs="1">
                            <xs:element ref="Byte"/>
                            <xs:element ref="Int16"/>
                            <xs:element ref="UInt16"/>
                            <xs:element ref="Int32"/>
                            <xs:element ref="UInt32"/>
                            <xs:element ref="Float32"/>
                            <xs:element ref="Float64"/>
                            <xs:element ref="String"/>
                            <xs:element ref="Url"/>
                            <xs:element ref="Grid"/>
                            <xs:element ref="Structure"/>
                            <xs:element ref="Sequence"/>
                        </xs:choice>
                        <xs:element name="dimension" type="dap:ArrayDimension" minOccurs="1"
                            maxOccurs="unbounded"/>
                    </xs:sequence>
                </xs:extension>
            </xs:complexContent>
        </xs:complexType>
        <!--

        -->
        <xs:complexType name="ArrayDimension">
            <xs:attribute name="name" type="xs:string"/>
            <xs:attribute name="size" type="xs:integer" use="required"/>
        </xs:complexType>
        <!--

        -->
        <xs:complexType name="Grid">
            <xs:complexContent>
                <xs:extension base="dap:BaseType">
                    <xs:sequence>
                        <xs:element ref="Array" minOccurs="1" maxOccurs="1"/>
                        <xs:element ref="Map" minOccurs="1" maxOccurs="unbounded"/>
                    </xs:sequence>
                </xs:extension>
            </xs:complexContent>
        </xs:complexType>
        <!--

        -->
        <xs:complexType name="Structure">
            <xs:complexContent>
                <xs:extension base="BaseType">
                    <xs:group ref="allDapTypes" minOccurs="1" maxOccurs="unbounded"/>
                </xs:extension>
            </xs:complexContent>
        </xs:complexType>
        <!--

        -->
        <xs:complexType name="Sequence">
            <xs:complexContent>
                <xs:extension base="BaseType">
                    <xs:group ref="allDapTypes" minOccurs="1" maxOccurs="unbounded"/>
                </xs:extension>
            </xs:complexContent>
        </xs:complexType>
        <!--

         -->
    </xs:schema>