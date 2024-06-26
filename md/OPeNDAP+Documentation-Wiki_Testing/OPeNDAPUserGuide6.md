# Data and Data Models

Basic to the operation of OPeNDAP is the translation of data from one
format to another. An OPeNDAP server must read data on some disk and
translate it into an intermediate format for transmission to the client.
It is to the question of these formats to which we shall turn first.

## Data models

Any data set is made up of data and a data model. The data model defines
the size and arrangement of data values, and may be thought of as an
abstract representation of the relationship between one data value and
another. Though it may seem paradoxical, it is precisely this
relationship that defines the meaning of some number. Without the
context provided by a data model, a number does not represent anything.
For example, within some data set, it may be apparent that a number
represents the value of temperature at some point in space and time.
Without its neighboring temperature measurements, and without the
latitude, longitude, depth, and time, the same number means nothing.

As the model only defines an abstract set of relationships, two data
sets containing different data may share the same data model. For
example, the data produced by two different measurements with the same
instrument will use the same data model, though the values of the data
are different. Sometimes two models may be equivalent. For example, an
XBT measures a time series of temperature, but is usually stored as a
series of temperature and depth measurements. The temperature vs. time
model of the original data is equivalent to the temperature vs. depth
model of the stored data.

In a computational sense, a data model may be considered to be the data
type or collection of data types used to represent that data. A
temperature measurement might occur as half an entry in a sequence of
temperature and depth pairs. However the data model also includes the
scalar latitude, longitude and date that identify the time and place
where the temperature measurements were taken. Thus the data set might
be represented in a C-like syntax like this:

    Dataset {
       Float64 lat;
       Float64 lon;
       Int32 minutes;
       Int32 day;
       Int32 year;
       Sequence {
          Float64 depth;
          Float64 temperature;
       } cast;
    } xbt-station;

Example Data Description of XBT Station

In the above example, a data set is described that contains all the data
from a single XBT. The data set is called
<font color='green'>xbt-station</font>, and contains floating-point
representations of the latitude and longitude of the station, and three
integers that specify when the XBT was released. The
<font color='green'>xbt-station</font> contains a single sequence
(called <font color='green'>cast</font>) of measurements, which are here
represented as values for depth and
temperature([16](Wiki_Testing/OPeNDAPUserGuideFootNotes "wikilink")).

A different data model representing the same data might look like this
figure 6.1:

    Dataset {
       Structure {
          Float64 lat;
          Float64 lon;
       } location;
       Structure {
          Int32 minutes;
          Int32 day;
          Int32 year;
       } time;
       Sequence {
          Float64 depth;
          Float64 temperature;
       } cast;
    } xbt-station;

Example Data Description of XBT Station Using Structures

In this example, several of the data have been grouped, implying a
relation between them. The nature of the relationship is not defined,
but it is clear that <font color='green'>lat</font> and
<font color='green'>lon</font> are both components of
<font color='green'>location</font>, and that each measurement in the
<font color='green'>cast</font> sequence is made up of depth and
temperature values.

In these two examples, meaning was added to the data set only by
providing a more refined context for the data values. No other data was
added, but still the second example can be said to contain more
information than the first one.

These two examples are refinements of the same basic arrangement of
data. However, there is nothing that says that a completely different
data model can't be just as useful or just as accurate. For example, the
depth and temperature data, instead of being represented by a sequence
of pairs, as in figure 6.1 and figure 6.1 could be represented by a pair
of sequences or arrays, as in figure 6.1

    Dataset {
       Structure {
          Float64 lat;
          Float64 lon;
       } location;
       Structure {
          Int32 minutes;
          Int32 day;
          Int32 year;
       } time;
       Float64 depth[500];
       Float64 temperature[500];
    } xbt-station;

Example Data Description of XBT Station Using Arrays

The relationship between the depth and temperature variables is no
longer clear, but, depending on what sort of processing is intended,
this may not be that important a loss.

The choice of a computational data model to contain some data set
depends in many cases on the whims and preferences of the user, as well
as on the data analysis software to be used. Several different data
models may be equally useful for a given task. Of course, some data
models will contain more information about the data than others, but
this information can also be carried in a scientist's head.

Note that with a carefully chosen set of data type constructors, such as
those we've used in the preceding examples, a user can implement an
infinite number of data models. The examples above use the OPeNDAP
Dataset Descriptor Structure (DDS) format, which will become important
in later discussions of the details of the OPeNDAP Data Access Protocol.
The precise details of the DDS syntax are described in ([Section
6.4.1](Wiki_Testing/OPeNDAPUserGuide6 "wikilink")).

### Data Models and APIs

A data access Application Program Interface (API) is a library of
functions designed to be used by a computer program to read, write, and
sample data. Any given data access API can be said to define implicitly
some data model. That is, the functions that make up the API accept and
return data using a certain collection of computational data types:
multi-dimensional arrays might be required for some data, scalars for
others, lists for others. This collection of data types, and their use
constitute the data model represented by that API. (Or data
models---there is no reason an API cannot accommodate several different
models.)

Among others, OPeNDAP currently supports two very different data access
APIs: netCDF and JGOFS. The netCDF API is designed for access to gridded
data, but has some limited capacity to access sequence data. The JGOFS
API provides access to relational or sequence data. Both APIs support
access in several programming languages (at least C and Fortran) and
both provide extensive support for limiting the amount of data
retrieved. For example a program accessing a gridded dataset using
netCDF can extract a subsampled portion or hyperslab of that data.
Likewise, the JGOFS API provides a powerful set of operators which can
be used to specify which sequence elements to extract (for example, a
user could request only those values corresponding to data captured
between 12:01am and 11:59am) as well as masking certain parameters from
the returned elements so that only those parameters needed by the
program are returned.

### Translating Data Models

The problem of data model translation is central to the implementation
of OPeNDAP. With an effective data translator, an OPeNDAP program
originally designed to read netCDF data can have some access to data
sets that use an incompatible data model, such as JGOFS.

In general, it is not possible to define an algorithm that will
translate data from any model to any other, without losing information
defined by the position of data values or the relations between them.
Some of these incompatibilities are obvious; a data model designed for
time series data may not be able to accommodate multi-dimensional
arrays. Others are more subtle. For example, a sequence looks very
similar to a collection of lists in many respects. However, a sequence
is an *ordered* collection of data types, whereas a list implies no
order. However, there are many useful translations that can be done, and
there are many others that are still useful despite their inherent
information loss.

For example, consider a relational structure like the one in figure
6.1.2. This is similar to the examples in
([Section6.1](Wiki_Testing/OPeNDAPUserGuide6 "wikilink")), rearranged to
accommodate an entire cruise worth of temperature-depth measurements.
This is the sort of data type that the JGOFS API is designed to use.

    Dataset {
       Sequence {
          Int32 id;
          Float64 latitude;
          Float64 longitude;
          Sequence {
             Float64 depth;
             Float64 temperature;
          } xbt_drop;
       } station;
    } cruise;

Example Data Description of XBT Cruise

Note that each entry in the <font color='green'>cruise</font> sequence
is composed of a tuple of data values (one of which is itself a
sequence). Were we to arrange these data values as a table, they might
look like this:

    id   lat   lon   depth  temp
    1   10.8   60.8    0     70
                      10     46
                      20     34
    2   11.2   61.0    0     71
                      10     45
                      20     34
    3   11.6   61.2    0     69
                      10     47
                      20     34

This can be made into an array, although that introduces redundancy.

    id   lat   lon   depth  temp
    1   10.8   60.8    0     70
    1   10.8   60.8   10     46
    1   10.8   60.8   20     34
    2   11.2   61.0    0     71
    2   11.2   61.0   10     45
    2   11.2   61.0   20     34
    3   11.6   61.2    0     69
    3   11.6   61.2   10     47
    3   11.6   61.2   20     34

The data is now in a form that may be read by an API such as netCDF. But
consider the analysis stage. Suppose a user wants to see graphs of
station data. It is not obvious simply from the arrangement of the array
where a station stops and the next one begins. Analyzing data in this
format is not a function likely to be accommodated by a program that
uses the netCDF API.

## Data Access Protocol

The OPeNDAP Data Access Protocol (DAP) defines how an OPeNDAP client and
an OPeNDAP server communicate with one another to pass data from the
server to the client. The job of the functions in the OPeNDAP client
library is to translate data from the DAP into the form expected by the
data access API for which the OPeNDAP library is substituting. The job
of an OPeNDAP server is to translate data stored on a disk in whatever
format they happen to be stored in to the DAP for transmission to the
client.

The DAP consists of four components:

1.  An "intermediate data representation" for data sets. This is used to
    transport data from the remote source to the client. The data types
    that make up this representation may be thought of as the OPeNDAP
    data model.
2.  A format for the "ancillary data" needed to translate a data set
    into the intermediate representation, and to translate the
    intermediate representation into the target data model. The
    ancillary data in turn consists of two pieces:
    - A description of the shape and size of the various data types
      stored in some given data set. This is called the \new{Data
      Description Structure} (DDS).
    - Capsule descriptions of some of the properties of the data stored
      in some given data set. This is the \new{Data Attribute Structure}
      (DAS).
3.  A "procedure" for retrieving data and ancillary data from remote
    platforms.
4.  An "API" consisting of OPeNDAP classes and data access calls
    designed to implement the protocol,

The intermediate data representation and the ancillary data formats are
introduced in ([Section 6.3](Wiki_Testing/OPeNDAPUserGuide6 "wikilink"))
and ([Section 6.4](Wiki_Testing/OPeNDAPUserGuide6 "wikilink")), below.
The steps of the procedure are outlined in ([Section
5.1](Wiki_Testing/OPeNDAPUserGuide5 "wikilink")), and the OPeNDAP core
software is described in the
\[<http://www.opendap.org/support/docs.html/api/pguide-html/><cite>The
OPeNDAP Programmer's Guide</cite>\] .

## Data representation

There are many popular data storage formats, and many more than that in
use. These formats are optimized (it they are optimized at all) for data
storage, and are not generally suitable for data transmission. In order
to transmit data over the Internet, OPeNDAP must translate the data
model used by a particular storage format into the data model used for
transmission.

If the data model for transmission is defined to be general enough to
encompass the abstractions of several data models for storage, than this
intermediate representation--the transmission format--can be used to
translate between one data model and another.

The OPeNDAP data model consists of a fairly elementary set of base
types, combined with an advanced set of constructs and operators that
allows it to define data types of arbitrary complexity. This way, the
OPeNDAP data access protocol can be used to transmit data from virtually
any data storage format.

The elements of the OPeNDAP data access protocol are:

Base Types

These are the simple data types, like integers, floating point numbers,
strings, and character data.

Constructor

Types These are the more complex data types that can be constructed from
the simple base types. Examples are structures, sequences, arrays, and
grids.

Operators

Access to data can be operationally defined with operators defined on
the various data types.

External Data Representation

In order to transmit the data across the Internet, there needs to be a
machine-independent definition of what the various data types look like.
For example, the client and server need to agree on the most significant
digit of a particular byte in the message

These elements are defined in greater detail in the sections that
follow.

### Base Types

The OPeNDAP data model uses the concepts of variables and operators.
Each data set is defined by a set of one or more variables, and each
variable is defined by a set of attributes. A variable's *attributes*
---such as units, name and type---must not be confused with the data
*value* (or values) that may be represented by that variable. A variable
called <font color='green'>time</font> may contain an integer number of
minutes, but it does not contain a particular number of minutes until a
context, such as a specific event recorded in a data set, is provided.
Each variable may further be the object of an operator that defines a
subset of the available data set. This is detailed in ([Section
6.3.3](Wiki_Testing/OPeNDAPUserGuide6 "wikilink")).

Variables in the OPeNDAP DAP have two forms. They are either base types
or type constructors. Base type variables are similar to predefined
variables in procedural programming languages like C or Fortran (such as
<font color='green'>int</font> or
<font color='green'>integer\*4</font>). While these certainly have an
internal structure, it is not possible to access parts of that structure
using the DAP. Base type variables in the DAP have two predefined
attributes (or characteristics): name, and type. They are defined as
follows:

> Name
>
> A unique identifier that can be used to reference the part of the
> dataset associated with this variable.
>
> Type
>
> The data type contained by the variable. This can be one of
> <font color='green'>Byte</font>, <font color='green'>Int32</font>,
> <font color='green'>UInt32</font>, <font color='green'>Float64</font>,
> <font color='green'>String</font>, and
> <font color='green'>URL</font>\\. Where:
>
> > 1.  <font color='green'>Byte</font> is a single byte of data. This
> >     is the same as <font color='green'>unsigned char</font> in ANSI
> >     C\\.
> > 2.  <font color='green'>Int32</font> is a 32 bit two's complement
> >     integer---it is synonymous with long in ANSI C when that type is
> >     implemented as 32 bits.
> > 3.  <font color='green'>UInt32</font> is a 32 bit unsigned integer.
> > 4.  <font color='green'>Float64</font> is the IEEE 64 bit floating
> >     point data type.
> > 5.  <font color='green'>String</font> is a sequence of bytes
> >     terminated by a null character.
> > 6.  <font color='green'>Url</font> is a string containing an OPeNDAP
> >     URL. Please refer to ([Section
> >     2.1](Wiki_Testing/OPeNDAPUserGuide2 "wikilink")) for more
> >     information about these strings. A special
> >     <font color='green'>\*</font> operator is defined for a URL. If
> >     the variable <font color='green'>my-url</font> is defined as a
> >     URL data type, then <font color='green'>my-url</font> indicates
> >     the string spelling out the URL, and
> >     <font color='green'>\*my-url</font> indicates the data specified
> >     by the URL.

The declaration in a DDS of a variable of any of the base types is
simply the type of the variable, followed by its name, and a semicolon.
For example, to declare a <font color='green'>month</font> variable to
be a 32-bit integer, one would type:

    Int32 month;

### Constructor Types

Constructor types, such as arrays, structures, and lists, describe the
grouping of one or more variables within a dataset. These classes are
used to describe different types of relations between the variables that
comprise the dataset. For example, an array might indicate that the
variables grouped are all measurements of the same quantity with some
spatial relation to one another, whereas a structure might indicate a
grouping of measurements of disparate quantities that happened at the
same place and time.

There are six classes of type constructor variables defined by the
OPeNDAP DAP: lists, arrays, structures, sequences, functions, and grids.
The types are defined as:

> List
>
> The list type constructor is used to hold lists of 0 or more items of
> one type. Lists are specified using the keyword
> <font color='green'>list</font> before the variable's class, for
> example, <font color='green'>list int32</font> or
> <font color='green'>list grid</font>. Access to the elements of a list
> is possible using one of the three operators shown in
>
> > - list.<font color='green'>length</font>"
> >
> > Returns the integer length of the "list".
> >
> > - list.<font color='green'>nth</font> Returns the n"th member ofthe
> >   "list".
> > - "list.<font color='green'>member value</font> Returns true if the
> >   value is a member of the "list".
>
> > NOTE: The syntax of these operators differs between their use in a
> > C++ program and a constraint expression. The length of some list,
> > given by <font color='green'>list.length()</font> in a program,
> > would be <font color='green'>length(list)</font> in a constraint
> > expression. Similarly, in a constraint expression, the position of a
> > value in a list is given by <font color='green'>nth(list,
> > value)</font>, and the presence of a value is indicated by
> > <font color='green'>member(list, value)</font>. See ([<cite>
> > opd-client,constraint</cite>](http://www)) for more information
> > about constraint expressions.
>
> A list declaration to create a list of integers would look like the
> following:
>
>     List Int32 months;
>
> Array
>
> An array is a one dimensional indexed data structure as defined by
> ANSI C\\. Multidimensional arrays are defined as arrays of arrays. An
> array may be subsampled using subscripts or ranges of subscripts
> enclosed in brackets (<font color='green'>()</font>). For example,
> <font color='green'>temp\[3\]\[4\]</font> would indicate the value in
> the fourth row and fifth column of the <font color='green'>temp</font>
> array.\footnote{As in C, OPeNDAP array indices start at zero.} A chunk
> of an array may be specified with subscript ranges; the array
> <font color='green'>temp\[2:10\]\[3:4\]</font> indicates an array of
> nine rows and two columns whose values have been lifted intact from
> the larger <font color='green'>temp</font> array.
>
> A hyperslab may be selected from an array with a \new{stride} value.
> The array represented by
> <font color='green'>temp\[2:2:10\]\[3:4\]</font> would have only five
> rows; the middle value in the first subscript range indicates that the
> output array values are to be selected from alternate input array
> rows. The array <font color='green'>temp\[2:3:10\]\[3:4\]</font> would
> select from every third row, and so on. table 6.3.3 shows the syntax
> for array accesses including hyperslabs.
>
> To declare a $5x6$ array of floating point numbers, the declaration
> would look like the following:
>
>     Float64 data[5][6];
>
> In addition to its magnitude, every dimension of an array may also
> have a name. The previous declaration could be written:
>
>     Float64 data[height = 5][width = 6];
>
> Structure
>
> A Structure is a class that may contain several variables of different
> classes. However, though it implies that its member variables are
> related somehow, it conveys no relational information about them. The
> structure type can also be used to group a set of unrelated variables
> together into a single dataset. The "dataset" class name is a synonym
> for structure.
>
> A Structure declaration containing some data and the month in which
> the data was taken might look like this:
>
>        Structure {
>           Int32 month;
>           Float64 data[5][6];
>        } measurement;
>
> Use the $.$ operator to refer to members of a \class{Structure}. For
> example, <font color='green'>measurement.month</font> would identify
> the integer member of the \class{Structure} defined in the above
> declaration.
>
> Sequence
>
> A Sequence is an ordered set of variables each of which may have
> several values. The variables may be of different classes. Each
> element of a \class{Sequence} consists of a value for each member
> variable. Thus a \class{Sequence} can be represented as:
>
> <center>
>
> |          |     |          |
> |----------|-----|----------|
> | s_{0 0} | .   | s_{0 n} |
> | .        | .   | .        |
> | s_{i 0} | .   | s_{i n} |
>
> </center>
>
> Every instance of sequence $S$ has the same number, order, and class
> of member variables. A \class{Sequence} implies that each of the
> variables is related to each other in some logical way. For example, a
> sequence containing position and temperature measurements might imply
> that the temperature measurements were taken at the corresponding
> position. A sequence is different from a structure because its
> constituent variables have several instances while a structure's
> variables have only one instance (or value). Because a sequence has
> several values for each of its variables it has an implied
> \new{state}, in addition to those values. The state corresponds to a
> single element in the sequence.
>
> A \class{Sequence} declaration is similar to a \class{Structure}'s.
> For example, the following would define a \class{Sequence} that would
> contain many members like the \class{Structure} defined above:
>
>        Sequence {
>           Int32 month;
>           Float64 data[5][6];
>        } measurement;
>
> Note that, unlike an Array, a Sequence has no index. This means that a
> Sequence's values are not simultaneously accessible. Like a Structure,
> the variable <font color='green'>measurement.month</font> has a single
> value. The distinction is that this variable's value changes depending
> on the state of the Sequence.
>
> Grid
>
> A Grid is an association of an $N$ dimensional array with $N$ named
> vectors (one-dimensional arrays), each of which has the same number of
> elements as the corresponding dimension of the array. Each data value
> in the grid is associated with the data values in the vectors
> associated with its dimensions.
>
> As an example, consider an array of temperature values that is six
> columns wide by five rows long. Suppose that this array represents
> measurements of temperature at five different depths in six different
> locations. The problem is the indication of the precise location of
> each temperature measurement, relative to one
> another.([18](Wiki_Testing/OPeNDAPUserGuideFootNotes "wikilink"))
>
> If the six locations are evenly spaced, and the five depths are also
> evenly spaced, then the data set can be completely described using the
> array and two scalar values indicating the distance between adjacent
> vertices of the array. However, if the spacing of the measurements is
> *not* regular, as in [figure 6.3.2](:Image:grid.gif "wikilink") then
> an array will be inadequate. To adequately describe the positions of
> each of the points in the grid, the precise location of each volume
> and row must be described.
>
> <center>
>
> <figure>
> <img src="grid.gif" title="actual size" />
> <figcaption>actual size</figcaption>
> </figure>
>
> An Irregular Grid of Data.
>
> </center>
>
> The secondary vectors in the Grid data type provide a solution to this
> problem. Each member of these vectors defines a value for all the data
> values in the corresponding rank of the array. The value can represent
> location or time or some other quantity, and can even be a constructor
> data type. The following declaration would define a data type that
> could accommodate a structure like this:
>
>      Grid {
>           Float64 data[distance = 6][depth = 5];
>           Float64 distance[6];
>           Float64 depth[5];
>        } measurement;
>
>
> In the above example, an vector called
> <font color='green'>depth</font> would contain five values
> corresponding to the depths of each row of the array, while another
> vector called <font color='green'>distance</font> might contain the
> scalar distance between the location of the corresponding column, and
> some reference point. The <font color='green'>distance</font> array
> could also contain six (latitude, longitude) pairs indicating the
> absolute location of each column of the grid.
>
>
>         Grid {
>           Float64 data[distance = 6][depth = 5];
>           Float64 depth[5];
>           Array Structure {
>              Float64 latitude;
>              Float64 longitude;
>           } distance[6];
>        } measurement;

### Operators

Access to variables can be modified using operators. Each type of
variable has its own set of selection and projection operators which can
be used to modify the result of accessing a variable of that type. table
6.3.3 lists the types and the operators applicable to them. In the
table, operators have the meaning defined by ANSI C except as follows:
the array hyperslab operators are as defined by netCDF\citel{netcdf},
the string operators are as defined by AWK\citel{kern:upe}, and the list
operators are as defined by Common Lisp\citel{steele:clisp}.

<center>

Constraint Expression Operators.

| Class                        | Operators                                    |
|------------------------------|----------------------------------------------|
| Simple Types                 |                                              |
| Byte, Int32, UInt32, Float64 | \< \> = != \<= \>=                           |
| String                       | = != ~=                                      |
| URL                          | \*                                           |
| Compound Types               |                                              |
| Array                        | \[start:stop\] \[start:stride:stop\]         |
| List                         | length(list), nth(list,n), member(list,elem) |
| Structure                    | \*                                           |
| Sequence                     | \*                                           |
| Grid                         | \[start:stop\] \[start:stride:stop\] .       |

</center>

Two of the operators deserve special note. Individual fields of type
constructors may be accessed using the dot
(<font color='green'>.</font>) operator or the virtual file system
syntax. If a structure <font color='green'>s</font> has two fields
<font color='green'>time</font> and
<font color='green'>temperature</font>, then those fields may be
accessed using <font color='green'>s.time</font> and
<font color='green'>s.temperature</font> or as
<font color='green'>s/time</font> and
<font color='green'>s/temperature</font>. Also, a special dereferencing
<font color='green'>\*</font> operator is defined for a URL. This is
roughly analogous to the pointer-dereference operator of ANSI C. That
is, if the variable <font color='green'>my-url</font> is defined as a
URL data type, then <font color='green'>my-url</font> indicates the
string spelling out the URL, and <font color='green'>\*my-url</font>
indicates the actual data indicated by the URL.

More information about variables and operators can be found in the
discussion of constraint expressions in ([Section
4.1](Wiki_Testing/OPeNDAPUserGuide4 "wikilink")).

### External Data Representation

Each of the base-type and type constructor variables has an external
representation defined by the OPeNDAP data access protocol. This
representation is used when an object of the given type is transferred
from one computer to another. Defining a single external representation
simplifies the translation of variables from one computer to another
when those computers use different internal representations for those
variable types.

<center>

The XDR data types corresponding to OPeNDAP base-type variables}

| Base Type | XDR Type          |
|-----------|-------------------|
| Byte      | xdr byte          |
| Int32     | xdr long          |
| UInt32    | xdr unsigned long |
| Float64   | xdr double        |
| String    | xdr string        |
| URL       | xdr string        |

</center>

Constraint expressions do not affect *how* a base-type variable is
transmitted from a client to a server; they determine *if* a variable is
to be transmitted. For constructor type variables, however, constraint
expressions may be used to exclude portions of the variable. For
example, if a constraint expression is used to select the first three of
six fields in a structure, the last three fields of that structure are
not transmitted by the server.

The data access protocol uses Sun Microsystems' XDR protocol\citel{xdr}
for the external representation of all of the base type variables.
\tableref{data,tab,base-xdr} shows the XDR types used to represent the
various base type variables.

In order to transmit constructor type variables, the data access
protocol defines how the various base type variables, which comprise the
constructor type variables, are transmitted. Any constructor type
variable may be subject to a constraint expression which changes the
amount of data transmitted for the variable (see ([Section
4.1](Wiki_Testing/OPeNDAPUserGuide4 "wikilink")) for more information
about constraint expressions.). For each of the six constructor types
these definitions are:

> Array
>
> An Array is sent using the <font color='green'>xdr_array</font>
> function. This means that an \class{Array} of 100
> <font color='green'>Int32</font>s is sent as a single block of 100
> <font color='green'>xdr long</font>s, not 100 separate "xdr long"s.
>
> List
>
> A List is sent as if it were an Array
>
> Structure
>
> A Structure is sent by encoding each field in the order those fields
> are declared in the DDS and transmitting the resulting block of bytes.
>
> Sequence
>
> A Sequence is transmitted by encoding each item in the sequence as if
> it were a Structure, and
>
> `ending each such structure after the other, in the order of their occurrence in the sequence. The entire sequence is sent, subject to the constraint expression. In other words, if no constraint expression is supplied then the entire sequence is sent. However, if a constraint expression is given all the records in the sequence that satisfy the expression are sent.`
>
> Grid
>
> A Grid is encoded as if it were a Structure (one component after the
> other, in the order of their declaration).

The external data representation used by an OPeNDAP server and client
may be compressed, depending on the configuration of the respective
machines. The compression is done using the
<font color='green'>gzip</font> program. Only the data transmission
itself will be affected by this; the transmission of the ancillary data
is not compressed.

## Ancillary data

In order to use some data set, a user must have some information at his
or her disposal that is not strictly included in the data set itself.
This information, called ancillary data
([20](Wiki_Testing/OPeNDAPUserGuideFootNotes "wikilink")), describes the
shape and size of the data types that make up the data set, and provides
information about many of the data set's attributes, as well. OPeNDAP
uses two different structures, to supply this ancillary information
about an OPeNDAP data set. The Dataset Descriptor Structure (DDS)
describes the data set's structure and the relationships between its
variables, and the Dataset Attribute Structure (DAS) provides
information about the variables themselves. Both structures are
described in the following sections.

### Dataset Descriptor Structure

In order to translate data from one data model into another, OPeNDAP
must have some knowledge about the types of the variables, and their
semantics, that comprise a given data set. It must also know something
about the relations of those variables---even those relations which are
only implicit in the dataset's own API. This knowledge about the
dataset's structure is contained in a text description of the dataset
called the Dataset Description Structure.

The DDS does not describe how the information in the dataset is
physically stored, nor does it describe how the data set API is used to
access that data. Those pieces of information are contained in the API
itself and in the OPeNDAP server, respectively. The server uses the DDS
to describe the structure of a particular dataset to a translator---the
DDS contains knowledge about the dataset variables and the
interrelations of those variables. In addition, the DDS can be used to
satisfy some of the DODS-supported API data set description calls. For
example, netCDF has a function which returns the names of all the
variables in a netCDF data file. The DDS can be used to get that
information.

The DDS is a textual description of the variables and their classes that
make up some data set. The DDS syntax is based on the variable
declaration and definition syntax of C and C++. A variable that is a
member of one of the base type classes is declared by writing the class
name followed by the variable name. The type constructor classes are
declared using C's brace notation. A grammar for the syntax is given in
[table 6.4.1](Wiki_Testing/OPeNDAPUserGuide6 "wikilink"). Each of the
keywords for the type constructor and base type classes have already
been described in ([Section
6.3](Wiki_Testing/OPeNDAPUserGuide6 "wikilink")). The
<font color='green'>Dataset</font> keyword has the same syntactic
function as Structure but is used for the specific job of enclosing the
entire data set even when it does not technically need an enclosing
element.

| "data set    | <font color='green'>Dataset</font> <font color='green'>{ declarations } name;                                                                                                                      |
|--------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| "declaration | <font color='green'>List</font> declaration                                                                                                                                                        |
|              | "base-type" var;                                                                                                                                                                                   |
|              | <font color='green'>Structure</font> {"declarations"} "var";                                                                                                                                       |
|              | <font color='green'>Sequence</font> {"declarations"} "var";                                                                                                                                        |
|              | <font color='green'>Grid</font> { <font color='green'>ARRAY</font> <font color='green'>:</font> "declaration" <font color='green'>MAPS</font>: "declarations" <font color='green'> } </font>"var"; |
| "base-type"  | Byte                                                                                                                                                                                               |
|              | <font color='green'>Int32</font>                                                                                                                                                                   |
|              | <font color='green'>UInt32</font>                                                                                                                                                                  |
|              | <font color='green'>Float64</font>                                                                                                                                                                 |
|              | <font color='green'>String</font>                                                                                                                                                                  |
|              | <font color='green'>Url</font>                                                                                                                                                                     |
| "var"        | "name"                                                                                                                                                                                             |
|              | "name array-decl"                                                                                                                                                                                  |
| "array-decl" | \[integer \]                                                                                                                                                                                       |
|              | <font color='green'>\["name" = integer\]</font>                                                                                                                                                    |
| "name"       | User-chosen name of data set, variable, or array dimension.                                                                                                                                        |

Dataset Descriptor Structure Syntax

Different data access APIs will store the information in the DDS in
different places. Some APIs are self-documenting in the sense that the
data files themselves will contain all the information about the
structure of their data types. Other APIs need secondary files
containing what is called ancillary data, describing the data structure.
For some APIs, such as netCDF, gathering the ancillary information from
the data archive may be a time-consuming process. The OPeNDAP server for
these APIs may cache ancillary data files to save time. An example DDS
entry is shown in
![Image:data,fig,dds](data,fig,dds "Image:data,fig,dds"). (See ([<cite>
data,model</cite>](http://www)) for an explanation of the information
implied by the data model, and for several other DDS examples).


     Dataset {
      Int32 catalog_number;
      Sequence {
        String experimenter;
        Int32 time;
        Structure {
          Float64 latitude;
          Float64 longitude;
        } location;
        Sequence {
          Float64 depth;
          Float64 salinity;
          Float64 oxygen;
          Float64 temperature;
        } cast;
      } station;
    } data;

Example Dataset Descriptor Entry.

When creating a DDS to be kept in an ancillary file, you can use the
<font color='green'>\#</font> character as a comment indicator. All
characters after the<font color='green'>\#</font> on a line are ignored.

### Dataset Attribute Structure

The "Dataset Attribute Structure" (DAS) is used to store attributes for
variables in the dataset. An attribute is any piece of information about
a variable that the creator wants to bind with that variable *excluding*
the type and shape, which are part of the DDS. Attributes can be as
simple as error measurements or as elaborate as text describing how the
data was collected or
processed[(21)](Wiki_Testing/OPeNDAPUserGuideFootnotes "wikilink"). In
principle, attributes are not processed by software, other than to be
displayed. However, many systems rely on attributes to store extra
information that is necessary to perform certain manipulations of data.
In effect, attributes are used to store information that is used \`by
convention' rather than \`by design'. OPeNDAP can effectively support
these conventions by passing the attributes from data set to user
program via the DAS. Of course, OPeNDAP cannot enforce conventions in
datasets where they were not followed in the first place.

Similarly to the DDS, the actual location of the DAS storage will vary
from one API to another. Data files created with some APIs will contain
within themselves attribute information that can be contained in the
DAS. For these APIs, the DAS will be constructed dynamically by the
OPeNDAP server from data within the files.

Other data access APIs must have attribute information specified in an
ancillary data file. APIs that contain attribute information can have
that information enriched by the addition of these ancillary attribute
files. These files are typically stored in the same directory as the
data files, and given the same name as the data files, appended with
<font color='green'>.das</font>.

The syntax for attributes in a DAS is given in [table
6.4.2](Wiki_Testing/OPeNDAPUserGuide6 "wikilink"). Every attribute of a
variable is a triple: attribute name, type and value. Note that the
attributes specified using the DAS are different from the information
contained in the DDS. Each attribute is completely distinct from the
name, type, and value of its associated variable. The name of an
attribute is an identifier, following the normal rules for an identifier
in a programming language with the addition that the \`/' character may
be used. The type of an attribute may be one of: "Byte", "Int32",
"UInt32", "Float64", "String" or "Url". An attribute may be scalar or
vector. In the latter case the values of the vector are separated by
commas (,) in the textual representation of the DAS.

<center>

| "DAS"           | <font color='green'>Attributes</font> "{var-attr-list}"   |
|-----------------|-----------------------------------------------------------|
| "var-attr-list" | "var-attr"                                                |
|                 | "var-attr-list" "var-attr"                                |
|                 | (empty list)                                              |
| "var-attr"      | "variable" {"attr-list"}                                  |
|                 | "container" {var-attr-list}                               |
|                 | "global-attr"                                             |
|                 | "alias"                                                   |
| "global-attr"   | <font color='green'>Global</font> "variable" {"attr-list} |
| "attr-list"     | attr-triple;                                              |
|                 | "attr-list" "attr-triple"                                 |
|                 | "(empty list)"                                            |
| "attr-triple"   | attr-type attribute attr-val-vec;                         |
| "attr-val-vec"  | "attr-val"                                                |
|                 | "attr-val-vec", "attr-val"                                |
| "attr-val"      | numeric value                                             |
|                 | "variable"                                                |
|                 | "string"                                                  |
| "attr-type"     | "Byte"                                                    |
|                 | <font color='green'>Int32</font>                          |
|                 | <font color='green'>UInt32</font>                         |
|                 | <font color='green'>Float64</font>                        |
|                 | <font color='green'>String</font>                         |
|                 | <font color='green'>Url</font>                            |
| "alias"         | <font color='green'>Alias</font> "alias-name variable;"   |
| "variable"      | user-chosen variable name                                 |
| "attribute"     | user-chosen attribute name                                |
| "container"     | user-chosen container name                                |
| "alias-name"    | user-chosen alias name                                    |
|                 |                                                           |

</center>

Dataset Attribute Structure Syntax

When creating a DAS to be kept in an ancillary file, you can use the
<font color='green'>\#</font> character as a comment indicator. All
characters after the <font color='green'>\#</font> on a line are
ignored.

#### Containers

An attribute can contain another attribute, or set of attributes. This
is roughly comparable to the way compound variables can contain other
variables in the DDS. The container defines a new lexical scope for the
attributes it contains
[(22)](Wiki_Testing/OPeNDAPUserGuideFootnotes "wikilink").

Consider the following example:


     Attributes {
       Bill {
          String LastName "Evans";
          Byte Age 53;
          String DaughterName "Matilda";
          Matilda {
             String LastName "Fink";
             Byte Age 26;
          }
       }
    }

{An Example of Attribute Containers}

Here, the attribute <font color='green'>Bill.LastName</font> would be
associated with the string "Evans", and
<font color='green'>Bill.Age</font> with the number 53. However, the
attribute <font color='green'>Bill.Matilda.LastName</font> would be
associated with the string "Fink" and
<font color='green'>Bill.Matilda.Age</font> with the number 26.

Using container attributes as above, you can construct a DAS that
exactly mirrors the construction of a DDS that uses compound data types,
like "Structure" and "Sequence". Note that though the
<font color='green'>Bill</font> attribute is a container, it has
attributes of its own, as well. This exactly corresponds to the
situation where, for example, a "Sequence" would have attributes
belonging to it, as well as attributes for each of its member variables.
Suppose the sequence represented a single time series of measurements,
where several different data types are measured at each time. The
sequence attributes might be the time and location of the measurements,
and the individual variables might have attributes describing the method
or accuracy of that measurement.

#### Aliases

Building on the previous example, it might be true that it would be
convenient to refer to Matilda without prefixing every reference with
<font color='green'>Bill</font>. In this case, we can define an
\new{alias} attribute

as follows:


     Attributes {
       Bill {
          String LastName "Evans";
          Byte Age 53;
          String DaughterName "Matilda";
          Matilda {
             String LastName "Fink";
             Byte Age 26;
          }
       }
       Alias Matilda Bill.Matilda;
    }


{An Example of Attribute Alias}

By defining an equivalence between the alias
<font color='green'>Matilda</font> and the original attribute
<font color='green'>Bill.Matilda</font>, the string
<font color='green'>Matilda.Age</font> can be used with or without the
prefix <font color='green'>Bill</font>. In either case, the attribute
value will be 26.

#### Global Attributes

A "global attribute" is not bound to a particular identifier in a
dataset; these attributes are stored in one or more containers with the
name <font color='green'>Global</font> or ending with
<font color='green'>_Global</font>. Global attributes are used to
describe attributes of an entire dataset. For example, a global
attribute might contain the name of the satellite or ship from which the
data was collected. Here's an example:


     Attributes {
       Bill {
          String LastName "Evans";
          Byte Age 53;
          String DaughterName "Matilda";
          Matilda {
             String LastName "Fink";
             Byte Age 26;
          }
       }
       Alias Matilda Bill.Matilda;
       Global {
          String Name "FamilyData";
          String DateCompiled "11/17/98";
       }
    }

{An Example of Global Attributes}

Global attributes can be used to define a certain view of a dataset. For
example, consider the following DAS:


     Attributes {
       CTD {
          String Ship "Oceanus";
          Temp {
             String Name "Temperature";
          }
          Salt {
             String Name "Salinity";
          }
       }
       Global {
          String Names "OPeNDAP";
       }
       FNO_Global {
          String Names "FNO";
          CTD {
             Temp {
                String FNOName "TEMPERATURE";
             }
             Salinity {
                String FNOName "SALINITY";
             }
          }
          Alias T CTD.Temp;
          Alias S CTD.Salt;
       }
    }

{An Example of Global Attributes In Use}

Here, a dataset contains temperature and salinity measurements. To aid
processing of this dataset by some OPeNDAP client, long names are
supplied for the <font color='green'>Temp</font> and
<font color='green'>Salt</font> variables. However, a different client
(FNO) spells variable names differently. Since it is seldom practical to
come up with general-purpose translation tables
[(23)](Wiki_Testing/OPeNDAPUserGuideFootnotes "wikilink") , the dataset
administrator has chosen to include these synonyms under the
<font color='green'>FNO_Global</font> attributes, as a convenience to
those users.

Similar conveniences can be provided using the Alias feature. In the
example in [figure 6.4.2](:Image:grid.gif "wikilink"), the temperature
variable can be referred to as <font color='green'>FNO_Global.T</font>
if desired. That is, a global alias can provide a client with a known
attribute name to query for some property, even if that attribute name
is not an integral part of the dataset.

Using global attributes, a dataset or catalog administrator can create a
layer of aliases and attributes to make OPeNDAP datasets conform to
several different dataset naming standards. This becomes significant
when trying to compile an OPeNDAP dataset database.