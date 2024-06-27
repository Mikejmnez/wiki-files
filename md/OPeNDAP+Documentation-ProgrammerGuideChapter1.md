# The OPeNDAP Client and Server Toolkit

The Distributed Oceanographic Data System (OPeNDAP) is a system used to
facilitate access to scientific data on the internet. Using OPeNDAP,
youcan turn any data analysis program that uses one of several data
access APIs into a powerful, internet-friendly data browser. For basic
information about the structure of OPeNDAP and its components, please
see [<cite>The OPeNDAP User
Guide</cite>](http://docs.opendap.org/index.php/Documentation).

The OPeNDAP Toolkit software consists of a collection of C++ classes
used to build OPeNDAP data servers and clients:

> Type classes : A complete implementation of the OPeNDAP data access protocol (DAP). This consists of a set of virtual classes for different types of data, which must be sub-classed to use.
>
> <!-- -->
>
> Data classes : This is a set of classes, including both the Data Descriptor Structure (DDS) and the Data Attribute Structure(DAS), designed to contain information about a dataset's data.
>
> <!-- -->
>
> Connect classes : These classes are used by a OPeNDAP client to mimic a durable connection to a OPeNDAP server.
>
> <!-- -->
>
> OPeNDAPFilter : This class consists of several utility functions useful for writing OPeNDAP servers.
>
> <!-- -->
>
> CGI Utilities : The file <font color='green'>cgi_util.h</font> describes a small number of other common functions needed by OPeNDAP servers.

The DAP toolkit contains three class hierarchies: one for each of the
DAS and DDS objects, rooted in classes with those names, and one for the
variables, rooted at the class BaseType. The following sections present
an overview of these class hierarchies and describe, in general terms,
how they are used to build the client-library and data server. See
([Section 1.1](ProgrammerGuideChapter1 "wikilink")) for information
about the DDS and DAS classes. To use the type classes, they must be
subclassed. [Chapter 3](ProgrammerGuideChapter3 "wikilink") provides a
detailed description of this process.

Detailed information about the DAS and DDS themselves is available in
the [<cite>The OPeNDAP User
Guide</cite>](http://docs.opendap.org/index.php/Documentation).
Descriptions of the classes and their member functions are in the
reference material, [<cite> The DODS Toolkit
Reference</cite>](http://www.opendap.org/api/pref/html/).

The OPeNDAP toolkit also contains two classes used by OPeNDAP clients to
manage network connections between themselves and a server. The Connect
object manages a single connection with some OPeNDAP server, and the
Connections object manages a group of Connect objects. The Connect class
is meant to be subclassed when used by a real client library. See
[Chapter 2](ProgrammerGuideChapter2 "wikilink").

## The DAS and DDS Objects

The dataset attribute structure (DAS) and dataset descriptor structure
(DDS) objects are used to store information about a data set's
variables. These objects are used on both the client and server sides,
although there are class features that only pertain to one or another of
the roles. They can be thought of as metadata objects. In this book,
however, we will avoid the term *metadata* because often this is *data*
to many users.

It might be said that neither the DAS nor the DDS contain actual science
data --- the DAS contains attribute information from the data set while
the DDS contains structural information about the data set and variables
in the data set. Since the boundary between data and metadata (or data
attributes) is often a blurry one, this is not a distinction we will
insist on.

To build both the DAS and DDS, the server either reads information
directly from the dataset or from OPeNDAP-specific ancillary data files,
depending on the capabilities of the data access API used to access the
data. The DAS and DDS server filter programs do this and then transmit
the resulting object to the client.

On the client side, the OPeNDAP
client[(2)](ProgrammerGuideFootnotes "wikilink") uses information in the
DAS and DDS to satisfy API calls issued by the user program requesting
information about variables, their type, shape, and attributes. The
client requests both of these objects when it first contacts the remote
data set. The DAS and DDS objects are then stored as part of a virtual
connection to that data set and can be used repeatedly by the client
library without retransmission.

The DAS and DDS objects have both an internal and an external
representation. Internal to the OPeNDAP client or server, these
structures are stored as C++ objects, while their external
representation is as text. The object is sent from the server to the
client using this text representation. Each of the two classes contains
a parser which can read the text representation and recreate the
object's internal representation. In addition, it is possible to write
the text representation for either object (using a text editor) and then
use the parser to create the internal, \Cpp, object. Furthermore, the
text representation is a type of persistence and can be used to build a
flexible object caching mechanism.

One possible use for this caching mechanism is to store the DAS and DDS
for a dataset and use the stored versions in place of opening the data
set and reading information about it and its contents. For large data
sets with many variables this can result in a significant performance
improvement, and several of the packaged OPeNDAP servers use it.

The caching mechanism may also be used on the server-side to store extra
information about the data set---information that is not present in the
data set proper, but which the data provider would like included when
people access the data set via OPeNDAP In this scenario, the data server
first integrates the data set file(s) using the API and builds the DAS
and DDS. Once an initial version of the DAS and DDS are resident in
memory, the parser is used to read an external text file which contains
additions to, or corrections of, the information extracted from the data
file. This information is the ancillary data introduced above. Several
of the OPeNDAP servers use this mechanism.

### The DAS Object

The DAS contains attribute information that is generally *not* used by
software when processing the variables; this object is specifically
designed to hold all the information in a data set that has no where
else to go. Each variable can have an unlimited number of attributes.
There are also \`global' attributes that apply to the dataset as a
whole. Each attribute is a set of three elements: the attribute name,
type and value. The supported types for an attribute are: Byte, Int32,
Float64, String and URL, and vectors of these types. These types have
the same range of values as the corresponding types in the data access
protocol.

A variable's attributes can be any qualities that are not part of the
DDS. Thus, the type and shape of a variable are *not* attributes; they
are characteristics of the variable and are part of the DDS (see
[Section 1.1.2](ProgrammerGuideChapter1 "wikilink")). However, users may
store any other information as
attributes[(3)](ProgrammerGuideFootnotes "wikilink") regardless,
including paragraphs of text, vectors of integers and floating point
values, and so on. There are some standard attributes required by the
OPeNDAP standard. The system *could* work without these attributes, but
they are required anyway to make other aspects of the systems work
better.

Many APIs support the concept of information in the data set that is not
structured, and provide function calls to access that information. The
DAS object provides a place for all that information. The data server
can interrogate the data set and build the DAS and the client-library
can use the DAS object to satisfy many of the API calls requesting
information about the variables in a data set.

[Figure 1.1.1](:Image:DAS.gif "wikilink") is a diagram of the DAS
object. The DAS consists of the following components:

- A list of attribute tables, each of which is a list of

attributes for a particular data variable. Since data variables can
contain other data variables (as with a compound data type, for
example), an attribute table can contain other attribute tables.

- A parser and scanner to read a text version of the

DAS object and create the corresponding C++ (binary) representation in
memory.

- A printer with which you can create a text version of the

DAS object.

<center>

<figure>
<img src="DAS.gif" title="actual size" />
<figcaption>actual size</figcaption>
</figure>

A DAS Object

</center>

The DAS printer (<font color='green'>DAS::print()</font>) is used to
create a textual representation of the C++ object. The object is
transmitted from server to client using this representation. The scanner
and parser (<font color='green'>DAS::parse()</font>) reads the printed
representation and creates a C++ object to match this specification.
This object method can be used to read the text version from a disk or
from a network connection, depending on the input stream identified for
it.

The third part of the DAS, the attribute table list, points to a series
of attribute tables. An attribute table is a list of name-type-value
triples used to describe a data variable. Each attribute describes some
aspect of the data variable. The example list in [Figure
1.1.1](:Image:DAS.gif "wikilink") shows what a table might look like for
one of the data variables in [Figure 1.1.1](:Image:DAS.gif "wikilink").
(The types have been left out of the diagrams for clarity.)

<center>

<figure>
<img src="attrtable.gif" title="actual size" />
<figcaption>actual size</figcaption>
</figure>

An Attribute Table

</center>

The DAS object contains member functions to add or retrieve AttrTable
objects as well as individual attributes based on a variable name. In
addition, the AttrTable object may be traversed using a Pix
[(4)](ProgrammerGuideFootnotes "wikilink").

The value of an attribute can itself be another attribute table. So, for
example, an aggregate variable that contains other variables, such as a
Structure, might have an attribute table for each of its member
variables. In [Figure 1.1.1](:Image:DAS.gif "wikilink"), you can see
illustrated the attributes of an aggregate data variable. The first
three attributes apply to the collection of data (it was taken with a
CTD instrument, from the good ship R/V Endeavour, and so on), while the
next three attributes reveal attributes of the constituent data
variables. Each of the values of these attributes is itself an attribute
table, containing attribute data about that data type.

<center>

<figure>
<img src="nest-attrtable.gif" title="actual size" />
<figcaption>actual size</figcaption>
</figure>

An Nested Attribute Table

</center>

OPeNDAP attribute tables are modelled with the AttrTable class. Each
AttrTable object contains a doubly-linked list of attribute triples, of
a structure named <font color='green'>entry</font>. Each entry object
contains a Name, Type and vector of values. AttrTable provides methods
for reading, writing, and modifying the attribute table.

The OPeNDAP definition of dataset attributes contains a "Global"
attribute. This has nothing to do with the data structure of the DAS
object, but with its use in OPeNDAP. Global attributes apply to all the
variables in the dataset. They can also be thought of as being
attributes of the dataset itself.

### The DDS Object

The DDS is used to store information about the organization of the data
set and its variables. It contains information about the type and shape
of variables. While the DDS is similar to the DAS in that it is used to
store information about the data set, it is used quite differently by
both the client and server components of OPeNDAP. The DAS is a
stand-alone object and is used solely for the purpose of storing
attributes of variables and the dataset. The DDS, however, stores type
information about a data set's variables by storing actual instances of
those variables.

The OPeNDAP \dap variable objects have methods that can be used to read
values from a data set or transfer the variable's value over the
network. This makes it convenient to use the DDS object itself to hold
data, and on the server side, the DDS object is used by both the DDS
filter program and the data filter program. (See [Section
4.1](ProgrammerGuideChapter4 "wikilink")) for more information about the
structure of the OPeNDAP server.)

<center>

<figure>
<img src="DDS.gif" title="actual size" />
<figcaption>actual size</figcaption>
</figure>

A DDS Object

</center>

[Figure 1.1.2](:Image:DAS.gif "wikilink") shows the structure of the DDS
class objects. The DDS consists of the following components:

- A String object used to store the name of the dataset to

which this DDS refers.

- A singly-linked list to store the variables in the data set.

Each variable is stored in an instance of one of the BaseType class's
descendants, and can be a simple or compound data type.

- A "printer" method to create a textual representation of the

DDS. Among other uses, this is used to transmit the DDS from server to
client.

- A parser and scanner to read a text DDS and convert it

into its C++ form. This is used to read DDS information from a disk, and
also to receive it over the network.

- A singly-linked list of parsed constraint expression clauses.

The clauses wait here to be evaluated.

- A constraint expression parser and scanner to extract constraint

expression clauses from the input constraint expression. This component
is responsible for creating the list of constraint expression clauses.

The DDS object provides methods to access and operate each of these
components. The two lists can be traversed with a g++ Pix object.

The DDS is \`lexically scoped' so that two Structure variables may have
components with the same names; each component will be referred to using
the Structure name and the dot operator. So for example, if a DDS called
<font color='green'>ralph</font> contains two structures,
<font color='green'>vitals</font> and
<font color='green'>new_hip</font>, and each structure contains a
variable called <font color='green'>age</font>, you can differentiate
the two by referring to one as
<font color='green'>ralph.vitals.age</font> and the other as
<font color='green'>ralph.new_hip.age</font>.

## The Type Hierarchy

The Type Hierarchy is the set of classes that form the hierarchy used to
build objects that contain data. These classes comprise the data model
for OPeNDAP. They contain simple data types such as integer and floating
point values as well as compound types like structure and sequence. Each
type is embodied by a C++ class, and the classes are arranged in a class
hierarchy, with a BaseType defining properties inherited by all the type
classes.

This section contains a brief description of the different types, their
relation to one another, and how they are used in an application
program. For detailed descriptions of the characteristics of each type,
including inheritance diagrams, please see [<cite> The OPeNDAP Toolkit
Reference</cite>](http://www.opendap.org/support/docs.html/api/pref/html/).

The OPeNDAP types can be divided into four categories:

- BaseType
- Simple Types

| Byte | Int16 |     | UInt16 | Int32 | UInt32 | Float32 | Float64 | Str | Url |
|------|-------|-----|--------|-------|--------|---------|---------|-----|-----|

- Vector Types

| List | Array |
|------|-------|

- Compound Types

| Structure | Sequence | Grid |
|-----------|----------|------|

Unlike the other classes in the OPeNDAP toolkit, the type classes are
abstract classes---in order to be used by a program, you must subclass
the hierarchy and create concrete classes to instantiate.

### Common Ancestor: BaseType

The root of the type hierarchy is the abstract class BaseType. This
class, because it is abstract, is never instantiated itself. BaseType is
used as the base class for all of the different types of variables, and
contains common member functions used by all the other type classes. For
simple variables such as Int32, only the abstract virtual functions in
BaseType need to be added to complete the class definition. Compound
types like Structure usually require more. A compound type contains one
or more instances of BaseType, and requires methods to access, add and
remove these member variables.

### Simple Types

The OPeNDAP simple data types consist of *Byte , Int16, UInt16, Int32,
UInt32 , Float32, Float64, Str* and *Url*. These data types match very
closely the corresponding types in C or Fortran. Note
that---internally---each of these types uses either the C or C++
representation to hold the value of the object.

These abstract classes are direct descendents of BaseType which contain
only their definitions for BaseType's abstract member functions and a
single constructor.

> \[Byte\] : Variables which store bytes. Equivalent to unsigned char on most UNIX workstations.
>
> <!-- -->
>
> \[Int16\] : Variables which store integer values as 16-bit twos-complement signed integers. Equivalent to <font color='green'>int</font> on most 16-bit UNIX workstations.
>
> <!-- -->
>
> \[UInt16\] : Unsigned integer. A 16-bit unsigned integer value.
>
> <!-- -->
>
> \[Int32\] : Variables which store integer values as 32-bit twos-complement signed integers. Equivalent to <font color='green'>int</font> on most 32-bit UNIX workstations.
>
> <!-- -->
>
> \[UInt32\] : Unsigned integer. A 32-bit unsigned integer value.
>
> <!-- -->
>
> \[Float32\] : Variables which store floating point data. Defined as the IEEE 32-bit floating point data type, equivalent to a <font color='green'>float</font> in ANSI C.
>
> <!-- -->
>
> \[Float64\] : Variables which store floating point data. Defined as the IEEE 64-bit floating point data type, equivalent to a <font color='green'>double</font> in ANSI C.
>
> <!-- -->
>
> \[Str\] : Variables which store string information. A OPeNDAP string is *not* a sequence of characters referenced using a pointer (as it is in C), it is represented using a C++ object of the class String.
>
> <!-- -->
>
> \[Url\] : Variables which store references to network resources. This is a sub-class of Str.

### Vector Types: Array, List

The vector data types are Array and List. A List is a simple ordering of
elements of a single type. An Array arranges the elements so that they
can be easily accessed with one or more indices.

> Array : Instances of Array have different semantics than arrays in C. They are multi-dimensional data structures which contain *N* instances of some data type. Each instance in the array can be referred to by an integer index between 0 and N-1. Multidimensional arrays are declared using C's syntax of a sequence of bracketed integer values: <font color='green'>Int32 a\[10\]\[20\]</font> declares an array of 10 arrays of 20 integers. However, unlike C arrays, the Array class supports named dimensions. In the preceding example, the array could have been declared: Int32 a\[row = 10\]\[col = 20\] where <font color='green'>row</font> and <font color='green'>col</font> are the names of the first and second dimension, respectively. You can use the <font color='green'>dimension_name</font> and <font color='green'>dimension_size</font> member functions of the Array class to determine the name and size for the $i^{th}$ dimension.
>
> <!-- -->
>
> : Array, like all of the compound types, contains a reference to a component variable. In the preceding example, the instance of Array would contain information about the dimensions of the array (10 by 20), but not the type of the elements (Int32). The element type information is stored in the component variable which the instance of Array references.
>
> <!-- -->
>
> : When creating an array, the dimension sizes (and optionally their names) must be set. Regardless of the shape of the array, it is always stored as a vector. In order to access the element of a multidimensional array it is necessary to calculate the offset for a given element.
>
> <!-- -->
>
> List : A List is an ordered collection of elements of unknown length. This is in contrast to the Array, whose size is always known in advance. When a List type is declared no size information is supplied, but in order to transmit a List object the length of that list must be known. Thus, internally the number of elements in the current value *is* stored.

### Compound Types: Structure, Sequence, Function, Grid

The compound data types are used to build new types as aggregates of
other types, including other compound types. (Note that List and Array
are compound types, as well, but contain only a single type of data.)
Structure , Sequence and Function all contain a list of BaseType
objects. However, they have different semantics; a Structure is a simple
aggregate; nothing other than aggregation is implied, while Sequence and
Function define templates for relational objects. A Grid combines
several Array objects so that nonlinear values may be applied to the
indices of an array.

> Structure : A Structure is an ordered collection of variables that conveys no relational information other than grouping. The variables that are members of a Structure may be of different types. In addition to the (possible) benefit of added organization, Structure may be used to supply information to the system that may be useful in optimizing the access or translation operations.
>
> <!-- -->
>
> Sequence : A Sequence is similar to a Structure in that it consists of an ordered collection of variables which may be of different types. However, where an instance of a Structure object describes a single set of data variables, an instance of a Sequence object describes a set of data variables, each of which is an entry in an ordered series of similar data variables.
>
> <!-- -->
>
> : Consider a Sequence named *S* , where each instance is called *s* :
>
> <center>
>
> | s <sub>0 0</sub> | s <sub>0 1</sub> | ... | s <sub>0 n</sub> |
> |------------------|------------------|-----|------------------|
> | s <sub>1 0</sub> | s <sub>1 1</sub> | ... | s <sub>0 n</sub> |
> | .                | .                | .   | .                |
> | s <sub>i 0</sub> | s <sub>i 1</sub> | ... | s <sub>i n</sub> |
> | .                | .                |     | .                |
>
> </center>
>
> : Every instance s_{i} of *S* has the same number, order, and class of variables. A Sequence implies that each of the *n* variables is related to each other in some logical way. Because a Sequence has several values for each of its variables it has an implied state, or position in the sequence, in addition to the instance data values.
>
> <center>
>
> Table of relational data.
>
> | Name}   | Age | Weight |
> |---------|-----|--------|
> | James   | 32  | 165    |
> | Charlie | 7   | 65.4   |
> | Bob     | 10  | 80     |
>
> </center>
>
> : For example given the the information in Table~(toolkit,seq2), s_{0} is <font color='green'>James</font>, <font color='green'>32</font>, <font color='green'>165</font>, s_{1} is <font color='green'>Charlie</font>, <font color='green'>7</font>, <font color='green'>65.4</font>, dots. The data in the table might have the following Sequence declaration:
>
> <center>
>
>     Sequence {
>         Str name;
>         Int32 age;
>         Float64 weight;
>     } people;
>
> </center>
>
> Grid : A Grid is an association of an *N* dimensional Array with *N* named vectors (map vectors), each of which has the same number of elements as the corresponding dimension of the Array. Each vector is used to map indices of one of the Array's dimensions to a set of values which are normally non-integral (e.g., floating point values). Two map vectors may be members of different classes.
>
> <center>
>
> | 32.0 | 31.5 | 31.1 | 30.8 | 29.2 |
> |------|------|------|------|------|
> | 32.3 | 31.8 | 31.4 | 30.9 | 29.8 |
> | 32.9 | 32.3 | 31.8 | 29.7 | 30.2 |
> | 32.3 | 31.8 | 31.5 | 30.7 | 29.9 |
>
> M =
>
> | 1.2 | 1.4 | 1.8 | 3.9 | 4.5 |
> |-----|-----|-----|-----|-----|
>
> N =
>
> | 67.8 | 68.7 | 92.3 | 95.2 |
> |------|------|------|------|
>
> {A sample Grid.}
>
> </center>
>
> In the table, the grid element indicated by
> <font color='green'>Grid\[2\]\[3\]</font> corresponds to
> <font color='green'>N\[2\]</font> and
> <font color='green'>M\[3\]</font>, or \$N = 92.3 and M = 3.9\$
> respectively. The element has a value of 29.7.