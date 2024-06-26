[Back to User Guide Contents](UserGuide "wikilink")

# The OPeNDAP Messages

An OPeNDAP server is in the business of making replies to queries for
data and other services. The queries come in the form of specially
formed URLs and the replies consist of MIME documents whose contents are
described in the sections below. (Technically speaking, the response
document headers aren't exactly right, but they function in the same
way.)

All the requests start with a root URL, and they all are in the form of
a GET, using a suffix on the root URL and a constraint expression to
indicate which service is requested and what the parameters are.

(There is also an experimental SOAP interface that uses a POST to
request data.)

The replies come in three categories: Ancillary data, Data, and the
other services. The following sections cover each of them, beginning
with the ancillary data messages.

## Ancillary data

In order to use some data set, a user must have some information at his
or her disposal that is not strictly included in the data itself. This
information, called ancillary data, describes the shape and size of the
data types that make up the data set, and provides information about
many of the data set's attributes, as well. OPeNDAP uses two different
structures, to supply this ancillary information about an OPeNDAP data
set. The Dataset Descriptor Structure (DDS) describes the data set's
structure and the relationships between its variables, and the Dataset
Attribute Structure (DAS) provides information about the variables
themselves. Both structures are described in the following sections.

### Dataset Descriptor Structure

In order to translate data from one data model into another, OPeNDAP
must have some knowledge about the types of the variables, and their
semantics, that comprise a given data set. It must also know something
about the relations of those variables—even those relations which are
only implicit in the dataset's own API. This knowledge about the
dataset's structure is contained in a text description of the dataset
called the "Dataset Description Structure" (DDS).

The DDS does not describe how the information in the data set is
physically stored, nor does it describe how the "native" API is used to
access that data. Those pieces of information are contained in the API
itself and in the OPeNDAP server, respectively. The DDS contains
knowledge about the dataset variables and the interrelations of those
variables. The server uses the DDS to describe the structure of a
particular dataset to a client.

The DDS is a textual description of the variables and their classes that
make up some data set. The DDS syntax is based on the variable
declaration and definition syntax of C and C++. A variable that is a
member of one of the base type classes is declared by writing the class
name followed by the variable name. The type constructor classes are
declared using C's brace notation. A grammar for the syntax is given in
the table below. (Note that the <font color='green'>Dataset</font>
keyword has the same syntactic function as Structure but is used for the
specific job of enclosing the entire data set even when it does not
technically need an enclosing element.)

| "data set    | <font color='green'>Dataset</font> <font color='green'>{ declarations } name;                                                                                                                      |
|--------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| "declaration | <font color='green'>List</font> declaration                                                                                                                                                        |
|              | "base-type" var;                                                                                                                                                                                   |
|              | <font color='green'>Structure</font> {"declarations"} "var";                                                                                                                                       |
|              | <font color='green'>Sequence</font> {"declarations"} "var";                                                                                                                                        |
|              | <font color='green'>Grid</font> { <font color='green'>ARRAY</font> <font color='green'>:</font> "declaration" <font color='green'>MAPS</font>: "declarations" <font color='green'> } </font>"var"; |
| "base-type"  | <font color="green">Byte</font>                                                                                                                                                                    |
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

<center>

Dataset Descriptor Structure Syntax

</center>

A DDS is returned in response to a request for the DDS, and it is also
part of the data return. The request URL for the DDS is composed of the
root URL, with the suffix ".dds". For example, if a data set is located
at <font
color="green"><http:/tests.opendap.org/data/mydata.dat></font> then
you'll find the DDS at <font
color="green"><http:/tests.opendap.org/data/mydata.dat.dds></font>

An example DDS entry is shown below. (There are more in
[Data_Models](UserGuideDataModel "wikilink"), where there's also an
explanation of the information implied by the data model.)

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

### Dataset Attribute Structure

The "Dataset Attribute Structure" (DAS) is used to store attributes for
variables in the dataset. An attribute is any piece of information about
a variable that the creator wants to bind with that variable *excluding*
the type and size, which are part of the DDS. Typical attributes might
range from error measurements to text describing how the data was
collected or processed.

In principle, attributes are not processed by software, other than to be
displayed. However, many systems rely on attributes to store extra
information that is necessary to perform certain manipulations of data.
In effect, attributes are used to store information that is used "by
convention" rather than "by design". OPeNDAP can effectively support
these conventions by passing the attributes from data set to user
program via the DAS. (Of course, OPeNDAP cannot enforce conventions in
datasets where they were not followed in the first place.0

The syntax for attributes in a DAS is given in the table below. Every
attribute of a variable is a triple: attribute name, type and value. The
name of an attribute is an identifier, consisting of alphanumeric
characters, plus "_" and "/". The type of an attribute may be one of:
"Byte", "Int32", "UInt32", "Float64", "String" or "Url". An attribute
may be scalar or vector. In the latter case the values of the vector are
separated by commas (,) in the textual representation of the DAS.

<center>

| "DAS"           | <font color="green">Attributes</font> "{var-attr-list}"    |
|-----------------|------------------------------------------------------------|
| "var-attr-list" | "var-attr"                                                 |
|                 | "var-attr-list" "var-attr"                                 |
|                 | (empty list)                                               |
| "var-attr"      | "variable" {"attr-list"}                                   |
|                 | "container" {var-attr-list}                                |
|                 | "global-attr"                                              |
| "global-attr"   | <font color="green">Global</font> "variable" {"attr-list"} |
| "attr-list"     | attr-triple;                                               |
|                 | "attr-list" "attr-triple"                                  |
|                 | "(empty list)"                                             |
| "attr-triple"   | attr-type attribute attr-val-vec;                          |
| "attr-val-vec"  | "attr-val"                                                 |
|                 | "attr-val-vec", "attr-val"                                 |
| "attr-val"      | numeric value                                              |
|                 | "variable"                                                 |
|                 | "string"                                                   |
| "attr-type"     | "Byte"                                                     |
|                 | <font color="green">Int32</font>                           |
|                 | <font color="green">UInt32</font>                          |
|                 | <font color="green">Float64</font>                         |
|                 | <font color="green">String</font>                          |
|                 | <font color="green">Url</font>                             |
| "variable"      | user-chosen variable name                                  |
| "attribute"     | user-chosen attribute name                                 |
| "container"     | user-chosen container name                                 |
|                 |                                                            |

</center>

Dataset Attribute Structure Syntax

A DAS is returned in response to a request for the DAS. Unlike the DDS,
it is not part of the data return. The request URL for the DAS is
composed of the root URL, with the suffix ".das". For example, if a data
set is located at <font
color="green"><http:/tests.opendap.org/data/mydata.dat></font> then
you'll find the DAS at <font
color="green"><http:/tests.opendap.org/data/mydata.dat.das></font>

#### Containers

An attribute can contain another attribute, or a set of attributes. This
is roughly comparable to the way compound variables can contain other
variables in the DDS. The container defines a new lexical scope for the
attributes it contains.

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

An Example of Attribute Containers

Here, the attribute <font color="green">Bill.LastName</font> would be
associated with the string "Evans", and
<font color="green">Bill.Age</font> with the number 53. However, the
attribute <font color="green">Bill.Matilda.LastName</font> would be
associated with the string "Fink" and
<font color="green">Bill.Matilda.Age</font> with the number 26.

Using container attributes as above, you can construct a DAS that
exactly mirrors the construction of a DDS that uses compound data types,
like "Structure" and "Sequence". Note that though the
<font color="green">Bill</font> attribute is a container, it has
attributes of its own, as well. This exactly corresponds to the
situation where, for example, a "Sequence" would have attributes
belonging to it, as well as attributes for each of its member variables.
Suppose the Sequence represented a single time series of measurements,
where several different data types are measured at each time. The
Sequence attributes might be the time and location of the measurements,
and the individual variables might have attributes describing the method
or accuracy of that measurement.

#### Global Attributes

A "global attribute" is not bound to a particular identifier in a
dataset; these attributes are stored in one or more containers with the
name <font color="green">Global</font> or ending with
<font color="green">_Global</font>. Global attributes are used to
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
       Global {
          String Name "FamilyData";
          String DateCompiled "11/17/98";
       }
    }

An Example of Global Attributes

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
       }
    }

An Example of Global Attributes In Use

Here, a dataset contains temperature and salinity measurements. To aid
processing of this dataset by some OPeNDAP client, long names are
supplied for the <font color="green">Temp</font> and <font
color="green">Salt</font> variables. However, a different client (FNO)
spells variable names differently. Since it is seldom practical to come
up with general-purpose translation tables, the dataset administrator
has chosen to include these synonyms under the <font
color="green">FNO_Global</font> attributes, as a convenience to those
users.

Using global attributes, a dataset or catalog administrator can create a
layer of attributes to make OPeNDAP datasets conform to several
different dataset naming standards. This becomes significant when trying
to compile an OPeNDAP dataset database.

## Data Transmission

An OPeNDAP server returns data to a client in response to a request URL
composed of the root URL, with the suffix ".dods". For example, if a
data set is located at <font
color="green"><http:/tests.opendap.org/data/mydata.dat></font> then
you'll find the data at <font
color="green"><http:/tests.opendap.org/data/mydata.dat.dods></font>

The data is returned in a MIME document that consists of two parts: the
[DDS](#Dataset_Descriptor_Structure "wikilink"), and the data encoded
according to the description in [External Data
Representation](UserGuideDataModel#External_Data_Representation "wikilink").
(The returned document is sometimes called the DataDDS.) The two parts
are separated by this string:

<font color="green" face="Courier">Data:<CR><NL></font>

The DDS included is modified according to any [constraint
expression](#Constraint_Expression "wikilink") that may have been
applied. That is, the returned DDS describes the returned data.

For example, consider a a request for data from a data set with a DDS
like this:

    Dataset {
        Grid {
          Array:
            Int16 sst[time = 1857][lat = 89][lon = 180];
          Maps:
            Float64 time[time = 1857];
            Float32 lat[lat = 89];
            Float32 lon[lon = 180];
        } sst;
        Float64 time_bnds[time = 1857][nbnds = 2];
    } sst.mnmean.nc;

This is the
[DDS](http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.dds) of a
typical gridded dataset. Suppose, though, that you ask for only the time
values of the data set. The DDS of the result will look like this:

    Dataset {
        Float64 time[time = 1857];
    } sst.mnmean.nc;

This
[DDS](http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.dds?time)
will be included in the DataDDS return, ahead of the encoded array of
1857 64-bit time values.

For more information about sampling OPeNDAP data sets, see the section
below about [constraint
expressions](#Constraint_Expressions "wikilink").

> NOTE:A request for data from an OPeNDAP client will generally make
> three different service requests, for data attributes (DAS), data
> descriptors (DDS), and for data. The prepackaged OPeNDAP clients do
> this for you, so you may not be aware that three requests are made for
> each URL.

## Other Services

In addition to the [data](#Data_Transmission "wikilink"),
[DDS](#Dataset_Descriptor_Structure "wikilink"), and
[DAS](#Dataset_Attribute_Structure "wikilink"), an OPeNDAP server *may*
provide any or all of the services described in the sections that
follow.

### ASCII Service

This service returns an ASCII representation of the requested data. This
can make the data available to a wide variety of standard web browsers.
This service is activated when the server receives a URL ending with
<font color="green">.asc</font> or <font
color="green">.ascii</font>.

Click
[1](http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.asc?sst%5B1:3)\[45:48\]\[90:95\]http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.asc?sst\[1:3\]\[45:48\]\[90:95\]\]
to see an ASCII response.

Note that unlike the data response, the ASCII response does not contain
a DDS for the returned data. Instead it just returns the simple text
message.

The [Quick Start Guide](QuickStart "wikilink") contains many examples of
the ASCII response.

### Info Service

The Info service formats information from a data set's DAS and DDS into
a single HTML document suitable for viewing in a web browser. The
returned document may include information about both the data server
itself (such as server functions implemented), and the dataset
referenced in the URL. The server administrator determines what
information is returned in response to such a request. The services is
activated by a URL ending in <font color="green">.info</font>.

Click <http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.info> to see
an Info response.

### WWW Interface Service

The WWW service is another way to browse a server's data interactively.
You can use this either to look at an individual data file, or a
directory of files (or "files" since a server may contain logical
entities that look like files).

The server uses a data set's DDS and DAS to construct a web form you can
use to construct a URL that subsamples the data set (using a [constraint
expression](#Constraint_Expressions "wikilink")). You can copy the
resulting URL into another browser, or use one of the buttons on the
form to download data.

<figure>
<img src="Reynolds_ifh.png" title="actual size" />
<figcaption>actual size</figcaption>
</figure>

If a server receives a URL that either ends in a slash
(<font color="green">/</font>) or
<font color="green">contents.html</font>, it returns a web page that
looks like a standard web browser's directory view.

<figure>
<img src="Test.oopendap.org_directory_view.png" title="actual size" />
<figcaption>actual size</figcaption>
</figure>

Each link in the directory view opens up the WWW service version of that
data.

The [Quick Start Guide](QuickStart#An_Easier_Way "wikilink") contains
more information about browsing OPeNDAP data interactively.

### Version Service

This service returns the version information for the OPeNDAP server
software running on the server. This service is triggered by a URL
ending with <font color="green">.ver</font>. The return is a short text
message containing the relevant version numbers.

### SOAP

The OPeNDAP server provided by the OPeNDAP group contains an
experimental SOAP service, allowing users to make requests and get
responses by exchanging SOAP XML documents. This is an experimental
service, and if you're writing an application depending on it, it's best
to contact the OPeNDAP developer team.

### DDX

The DDX is an XML version of the DAS and DDS, combined. It is triggered
by a URL ending with <font color="green">.ddx</font>. You can find the
schema for the DDX at
[<http://xml.opendap.org/dap/dap2.xsd>](http://xml.opendap.org/dap/dap2.xsd).

The DDX is designed to contain data, too, but this is not yet
implemented. You will see an empty dataBLOB element at the end of each
DDX, which will eventually hold returned data.

The DDX response can be modified with a [constraint
expression](#Constraint_Expressions "wikilink"). Like the DDS, the DDX
will describe only the data actually returned.

The DDX and the DataDDX (containing the dataBLOB) are the nucleus of
what will become version 4 of the DAP.

### THREDDS

Some OPeNDAP servers (including [Hyrax](Hyrax "wikilink"), the server
supplied by the OPeNDAP group) can make sensible replies to requests for
THREDDS catalog information. This can serve to "advertise" a server's
data by having it appear in catalogs accumulated by THREDDS browsers.
For more information about THREDDS, see the
[THREDDS](http://www.unidata.ucar.edu/projects/THREDDS/tech/TDS.html)
home page.

# Constraint Expressions

The OPeNDAP software is not only a data transport mechanism. Using
OPeNDAP, you can subsample the data you are looking at. That is, you can
request an entire data file, or just a small piece of it.

## Selecting Data: Using Constraint Expressions

The URL such as this one:

    http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz

refers to the entire dataset contained in the mnmean.nc file. A user
may, however, choose to sample the dataset simply by modifying the
submitted URL. The constraint expression attached to the URL directs
that the data set specified by the first part of the URL be sampled to
select only the data of interest from a dataset.

Because the expression modifies the URL used to access data, this works
even for programs that do not have a built-in way to accomplish such
selections. This can vastly reduce the amount of data a program needs to
process, and reduce the network load of transmitting that data to the
client.

### Constraint Expression Syntax

A constraint expression is appended to the target URL following a
question mark, as in the following examples:

[.../nc/sst.mnmean.nc.gz?sst\[3\]\[4\]\[5\]](http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.asc?sst%5B3%5D%5B4%5D%5B5%5D)

[2](http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.asc?sst%5B0:1)\[13:16\]\[103:105\]
.../nc/sst.mnmean.nc.gz?sst\[0:1\]\[13:16\]\[103:105\]\]

\[<http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.asc?geogrid(sst,62,206,56,210>,"19722\<time\<19755")
.../nc/sst.mnmean.nc.gz?geogrid(sst,62,206,56,210,"19722\<time\<19755")\]

[.../ff/gsodock.dat?Time,Sea_Temp](http://test.opendap.org/dap/data/ff/gsodock.dat.asc?Time,Sea_Temp)

[.../ff/gsodock.dat?Time,Sea_Temp&Time%3C35234.1](http://test.opendap.org/dap/data/ff/gsodock.dat.asc?Time,Sea_Temp&Time%3C35234.1)

[.../ff/gsodock.dat?Time,Sea_Temp&Time%3C35234.1&Sea_Temp%3C18](http://test.opendap.org/dap/data/ff/gsodock.dat.asc?Time,Sea_Temp&Time%3C35234.1&Sea_Temp%3C18)

> <font color="red">CAUTION:</font> An OPeNDAP data set can contain an
> extraordinary amount of data. You almost certainly do *not* want to
> make an unconstrained request to a data set without knowing something
> about it. Familiarize yourself with the DDS and DAS before asking for
> data; it can prevent overload.

A constraint expression consists of two parts: a projection and a
selection, separated by an ampersand (&). Either part may contain
several sub-expressions. Either part may be present, or both.

    ...?proj_1,proj_2,...,proj_n&sel_1&sel_2&...&sel_m

A projection is simply a comma-separated list of the variables that are
to be returned to the client. If an array is to be subsampled, the
projection specifies the manner in which the sampling is to be done. If
the selection is omitted, all the variables in the projection list are
returned. If the projection is omitted, the entire dataset is returned,
subject to the evaluation of the selection expression. The projection
can also include functional expressions of the form:

    ...?function(arg_1,arg_2,...,arg_n)

where the arguments are variables from the dataset, scalar values, or
other functions. (See [Constraint Expression
Functions](#Constraint_Expression_Functions "wikilink") below.)

A selection expression leads with an ampersand and is a boolean
expression of the form "variable operator variable", "variable operator
value" or "function(arg_1,arg_2,...,arg_n)", where

operator
Can be one of the relational operators listed in the table below.

variable
Can be any variable recorded in the dataset.

value
Can be any scalar, string, function, or list of numbers (Lists are
denoted by comma-separated items enclosed in curly braces ,for example,
{3,11,4.5}.); and

function
Is a function defined by the server to operate on variables or values,
and to return a boolean value.

Each selection clause begins with an ampersand (&). You can think of
this as representing the "AND" boolean operation, but remember that it
is actually a prefix operator, not an infix operator. That is, it must
appear at the beginning of each selection clause, no matter what. This
means that a constraint expression that contains no projection clause
must still have an & in front of the first selection clause.

There is no limit on the number of selection clauses that can be
combined to create a compound constraint expression. Data that produces
a true (non-zero) value for the entire selection expression will be
included in the data returned to the client by the server. If only a
part of some data structure, such as a Sequence, satisfies the selection
criteria, then only that part will be returned.

> NOTE: Due to the differences in data model paradigms, selection is not
> implemented for the OPeNDAP array data types, such as Grid or Array.
> However, many OPeNDAP servers implement selection functions you can
> use for the same effect. (See [Constraint Expression
> Functions](#Constraint_Expression_Functions "wikilink") below for more
> about functions.)

#### Simple Constraint Expression Examples

Consider the DDS below. This is the description of a dataset containing
station data including temperature, oxygen, and salinity. Each station
also contains 20 oxygen data points, taken at 20 fixed depths, used for
calibration of the data.

    Dataset {
       Sequence{
          Int32 day;
          Int32 month;
          Int32 year;
          Float64 lat;
          Float64 lon;
          Float64 O2cal[20];
          Sequence{
             Float64 press;
             Float64 temp;
             Float64 O2;
             Float64 salt;
          } cast;
          String comments;
       } station;
    } arabian-sea;

Sample Data Descriptor

The following URL will return only the pressure and temperature pairs of
this dataset. (Note that the constraint expression parser removes all
spaces, tabs, and newline characters before the expression is parsed.)
There is only a projection clause, without a selection, in this
constraint expression.

    http://oceans.edu/jg/exp1O2/cruise?station.cast.press,station.cast.temp

We have assumed that the dataset was stored in the JGOFS format on the
remote host oceans.edu, in a file called explO2/cruise. For the sake of
brevity, from here on we will omit the first part of the URL, to
concentrate on the constraint expression alone.

If we only want to see pressure and temperature pairs below 500 meters
deep, we can modify the constraint expression by adding a selection
clause.

    ?station.cast.press,station.cast.temp&station.cast.press>500.0

In order to retrieve all of each cast that has any temperature reading
greater than 22 degrees, use the following:

    ?station.cast&station.cast.temp>22.0

Simple constraint expressions may be combined into compound expressions
by appending them to one another. To retrieve all stations west of 60
degrees West and north of the equator:

    ?station&station.lat>0.0&station.lon<-60.0

As was mentioned, the logical OR can be implemented using a list of
scalars. The following expression will select only stations taken north
of the equator in April, May, June, or July.

    ?station&station.lat>0.0&station.month={4,5,6,7}

If our dataset contained a field called monsoon-month, indicating the
month in which monsoons happened that year, we could modify the last
example search to include those months as follows:

    ?station&station.lat>O.O&station.month={4,5,6,7,station.monsoon-month}

In other words, a list can contain both values and other variables. If
monsoon-month was itself a list of months, a search could be written as:

    ?station&station.lat>0.0&station.month=station.monsoon-month

For arrays and grids, there is a special way to select data within the
projection clause. Suppose we want to see only the first five oxygen
calibration points for each station. The constraint expression for this
would be:

    ?station.02cal[0:4]

By specifying a stride value, we can also select a hyperslab of the
oxygen calibration array:

    ?station.02cal[0:5:19]

This expression will return every fifth member of the 02cal array. In
other words, the result will be a four-element array containing only the
first, sixth, eleventh, and sixteenth members of the 02cal array. Each
dimension of a multi-dimensional arrays may be subsampled in an
analogous way. The return value is an array of the same number of
dimensions as the sampled array, with each dimension size equal to the
number of elements selected from it.

### Operators, Special Functions, and Data Types

The constraint expression syntax defines a number of operators for each
data type. These operators are listed in the table below.

All the operators defined for the scalar base types are boolean
operators whose result depends on the specified comparison between its
arguments.

<center>

Constraint Expression Operators.

| Class                        | Operators                              |
|------------------------------|----------------------------------------|
| **Simple Types**             |                                        |
| Byte, Int\*, UInt\*, Float\* | \< \> = != \<= \>=                     |
| String                       | = != ~=                                |
| URL                          | = != ~=                                |
| **Compound Types**           |                                        |
| Array                        | \[start:stop\] \[start:stride:stop\]   |
| Structure                    | . /                                    |
| Sequence                     | . /                                    |
| Grid                         | \[start:stop\] \[start:stride:stop\] . |

</center>

Individual fields of type constructors may be accessed using the dot
(<font
color='green'>.</font>) operator or the virtual file system syntax. If a
structure <font color='green'>s</font> has two fields <font
color='green'>time</font> and <font color='green'>temperature</font>,
then those fields may be accessed using
<font color='green'>s.time</font> and
<font color='green'>s.temperature</font> or as
<font color='green'>s/time</font> and
<font color='green'>s/temperature</font>.

The ~= operator returns true when the character string on the left of
the operator matches the regular expression on the right. See [Pattern
Matching with Constraint
Expressions](#Pattern_Matching_with_Constraint_Expressions "wikilink")
for a discussion of regular expressions.

The array operator \[\] is used to subsample the given array. You can
find several examples of its use in the [Quick Start
Guide](QuickStart#Peeking_at_Data "wikilink").

### Constraint Expression Functions

An OPeNDAP data server may define its own set of functions that may be
used in a constraint expression. For example, the oceans.edu data server
we've been imagining might define a sigma1() function to return the
density of the water at the given temperature, salinity and pressure. A
query like the following would return all the stations containing water
samples whose density exceeded 1.0275g/cm3.

    ?station.cast&sigma1(station.cast.temp,
                         station.cast.salt,
                         station.cast.press)>27.5

Functions like this one are not a standard part of the OPeNDAP
architecture, and may vary from one server to another. A user may query
a server for a list of such functions by sending a URL with a constraint
expression that calls the "version()" function.

This will return a list of functions implemented. Call any of the
functions with no arguments to see a description of the arguments.

For example:

<http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.asc?version>()

And:

<http://test.opendap.org/dap/data/nc/sst.mnmean.nc.gz.asc?geogrid>()

> Note: When using functions, be careful to remember that a function
> used in a projection can return any value, but when used in a
> selection clause, it must either return a boolean value, or be part of
> a test that returns a boolean value.

### Pattern Matching with Constraint Expressions

There are three operators defined to compare one String data type to
another. The = operator returns TRUE if its two input character strings
are identical, and the != operator returns TRUE if the Strings do not
match. A third operator, ~= is provided that returns TRUE if the String
to the left of the operator matches the regular expression in the String
on the right.

A regular expression is simply a character string containing wildcard
characters that allow it to match patterns within a longer string. For
example, the following constraint expression might return all the
stations on the sample cruise at which a shark was sighted:

    ?station&station.comment~=".*shark.*"

Most characters in a regular expression match themselves. That is, an
"f" in a regular expression matches an "f" in the target string. There
are several special characters, however, that provide more sophisticated
pattern-matching capabilities.

.
The period matches any single character except a newline.

\* + ?
These are postfix operators, which indicate to try to match the
preceding regular expression repetitively (as many times as possible).
Thus, o\* matches any number of o's. The operators differ in that o\*
also matches zero o's, o+ matches only a series of one or more o's, and
o? matches only zero or one o.

"\[ ... \]"
Define a "character set," which begins with \[ and is terminated by \].
In the simplest case, the characters between the two brackets are what
this set can match. The expression \[Ss\] matches either an upper or
lower case s. Brackets can also contain character ranges, so \[0-9\]
matches all the numerals. If the first character within the brackets is
a caret ( ), the expression will only match characters that do not
appear in the brackets. For example, \[ 0-9\]\* only matches character
strings that contain no numerals.

^\$
These are special characters that match the empty string at the
beginning or end of a line.

\\
These two characters define a logical OR between the largest possible
expression on either side of the operator. So, for example, the string
Endeavor\\Oceanus matches either Endeavor or Oceanus. The scope of the
OR can be contained with the grouping operators, $and$.

$$
These are used to group a series of characters into an expression, or
for the OR function. So, for example, $abc$\* matches zero or more
repetitions of the string abc2.

There are several more special characters and several other features of
the characters described here, but they are beyond the scope of this
guide. The OPeNDAP regular expression syntax is the same as that used in
the Emacs editor. See the documentation for Emacs for a complete
description of all the pattern- matching capabilities of regular
expressions.

### Optimizing the Query

Using the tools provided by OPeNDAP, a user can build quite elaborate
and sophisticated constraint expressions that will return precisely the
data he or she wishes to examine. However, as the complexity of the
constraint expression increases, so does the time necessary to process
that expression. There are some techniques a user may user to optimize
the evaluation of a constraint that will ease the load on the server,
and provide faster replies to OPeNDAP dataset queries.

The OPeNDAP constraint expression evaluator uses a "lazy evaluation"
algorithm. This means that the sub-clauses of the selection clause are
evaluated in order, and parsing halts when any sub-clause returns FALSE.
Consider a constraint expression that looks like this:

    ?station&station.cast.O2>15.0&station.cast.temp>22.0

If the server encounters a station with no oxygen values over 15.0, it
does not bother to look at the temperature records at all. The first
sub- clause evaluates FALSE, so the second clause is never even parsed.

A careful user may use this feature to his or her advantage. In the
above example, the order of the clauses does not really matter; there
are the same number of temperature and oxygen measurements at each
station. However, consider the following expression:

    ?station&station.cast.O2>15.0&station.month={3,4,5}

For each station there is only one month value, while there are many
oxygen values. Passing a constraint expression like this one will force
the server to sort through all the oxygen data for each station (which
could be in the thousands of points), only to throw the data away when
it finds that the month requested does not match the month value stored
in the station data. This would be far better done with the clauses
reversed:

    ?station&station.month={3,4,5}&station.cast.O2>15.0

This expression will evaluate much more quickly because unwanted
stations may be quickly discarded by the first sub-clause of the
selection. The server will only examine each oxygen value in the station
if it already knows that the station might be worth keeping.

This sort of optimization becomes even more important when one of the
clauses contains a URL. In general, any selection sub-clause containing
a URL should be left to the end of the selection. This way, the OPeNDAP
server will only be forced to go to the network for data if absolutely
necessary to evaluate the constraint expression.