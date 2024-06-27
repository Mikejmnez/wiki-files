### Goal

Provide an improved XML representation of DAP Array objects.

### Background

In the DAP2 DDX, arrays are represented as their own type, with an
embedded (and nameless) template variable. So this:

`       `<Array name="foo">
`           `<Int16/>
`           `<dimension name="i" size="2"/>
`           `<dimension name="j" size="3"/>
`       `</Array>

Represents 2x3 array of 16 bit integers, expressed like this a DDS:

`   Int16 foo[2][3];`

A "better", more intuitive, and more compact representation is proposed
here.

### Design

[jimg](User:Jimg "wikilink") 14:07, 23 January 2009 (PST) See
[\#Option_3](#Option_3 "wikilink") for my pick of the proposed
solutions.

We think a better array representation for our DDX would be:

`           `<Int16 name="foo">
`               `<dimension name="i" size="2"/>
`               `<dimension name="j" size="3"/>
`           `</Int16>

So basically any type that contains a dap:dimension element is an array.
This is fairly straight forward for simple types and Structures. Arrays
of Grids and Sequences are already disallowed. However, since the Grid
data type contains 2 or more arrays types that are used as the grid's
data array and Map vectors, the representation of Grid needs to be
modified to accommodate this change.

Here is an example of a DAP2 DDX:

`   `<Grid name="order">
`       `<Array name="order">
`           `<Int16/>
`           `<dimension name="i" size="2"/>
`           `<dimension name="j" size="3"/>
`       `</Array>
`       `<Map name="i">
`           `<Int32/>
`           `<dimension name="i" size="2"/>
`       `</Map>
`       `<Map name="j">
`           `<Float32/>
`           `<dimension name="j" size="3"/>
`       `</Map>
`   `</Grid>

#### Option 1

Add an optional "role" attribute to the simple types we could produce a
Grid like this:

`   `<Grid name="order">
`       `<Int16 name="order" role="gridArray" >
`           `<dimension name="i" size="2"/>
`           `<dimension name="j" size="3"/>
`       `</Int16 >
`       `<Int32 name="i" role="gridMap">
`           `<dimension name="i" size="2"/>
`       `</Int32 >
`       `<Float32 name="j" role="gridMap">
`           `<dimension name="j" size="3"/>
`       `</Float32 >
`   `</Grid>

Developing an XML schema for this might be quite challenging.

#### Option 2

Rely on document order to provide the differentiation between the grid
Array and the Map arrays. (not probably a good idea when you consider
that we want to allow multiple grid arrays in a grid.)

#### Option 3

[jimg](User:Jimg "wikilink") 14:00, 23 January 2009 (PST) I like this
the best.

Encapsulate the Maps and grid Arrays:

`   `<Grid name="order">
`       `<grids>
`           `<Int16 name="order">
`               `<dimension name="i" size="2"/>
`               `<dimension name="j" size="3"/>
`           `</Int16>
`       `</grids >
`       `<maps>
`           `<Int32 name="i">
`               `<dimension name="i" size="2"/>
`           `</Int32>

`           `<Float32 name="j" >
`               `<dimension name="j" size="3"/>
`           `</Float32>
`       `</maps>
`   `</Grid>

A schema for this Grid type might look like:

`  `<xs:complexType name="Grid">
`       `<xs:complexContent>
`           `<xs:extension base="dap2:BaseType">
`               `<xs:sequence>
`                   `<xs:element name="grids" minOccurs="1" maxOccurs="1" >
`                       `<xs:complexType>
`                           `<xs:choice>
`                               `<xs:group ref="simpleArrayTypes"  minOccurs="1" maxOccurs="1"   />
`                           `</xs:choice>
`                       `</xs:complexType>
`                   `</xs:element>
`                   `<xs:element name="maps" minOccurs="1" maxOccurs="1" >
`                       `<xs:complexType>
`                           `<xs:choice>
`                               `<xs:group ref="simpleArrayTypes"  minOccurs="1" maxOccurs="unbounded"   />
`                           `</xs:choice>
`                       `</xs:complexType>
`                   `</xs:element>
`               `</xs:sequence>
`           `</xs:extension>
`       `</xs:complexContent>
`   `</xs:complexType>

Although to be honest I don't think the structural semantics of the Grid
data type can be completely captured by an XML schema. For example, I
have no idea how to encode the following concepts in a schema:

- Every dimension of a grid Array must have a corresponding Map array
  with of the same size.
- Map arrays are associated with Grid array dimension by document order.
- Names of dimensions of grids and maps must agree.

[DAP3/4](Category:Development "wikilink")
[DAP3/4](Category:DAP4 "wikilink")