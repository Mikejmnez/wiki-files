# Preface

This tutorial describes the steps required to enable your client
application to interact with the OPeNDAP Data Access Protocol by using
the Java or C++ classes provided in the OPeNDAP class libraries. It also
describes the trade offs between using these toolkits and a
client-library interface such as netCDF. The C++ and Java toolkis are
class libraries which provide for direct interaction with a remote
OPeNDAP server. You'll need to write some code. If your client
application currently uses one of the netCDF, GDAL or OGR APIs, then you
only need to relink your application with the OPeNDAP-enabled version of
the API library, allowing you to skip this tutorial entirely.

# Writing your own OPeNDAP client

## Choose a language

The OPeNDAP project provides both
[C++](http://docs.opendap.org/index.php/ProgrammerGuide) and
[Java](http://www.opendap.org/api/javaDocs/index.html) implementations
of the DAP. Each library includes classes that implement the various
objects which comprise the DAP software for building clients. Each also
includes some extra software which simplifies building clients by
managing virtual connections, handling data caching, et cetera. To
choose one of the toolkits, several factors should be weighed. With
which of the two programming languages are you most comfortable? What
type of computer will the client run on? For client development, both
Java and C++ are supported on win32 and Unix architectures. Java is more
likely to be supported on other architectures, such as Mac.

The DAP library is middle-ware. You can use it to build a completely new
client or to add network data access to an existing program (making it a
client). If you're interested in writing a client from scratch, simply
choose the toolkit/language you feel most comfortable. If you're going
to take an existing program and transform it into a client there are
several additional factors beyond programming language you should
consider.

If the program you want to DAP-enable can read netCDF files, by far the
easiest way to achieve your goal is to use our DAP-enable netCDF *client
library* (CL). This piece of software works like the standard netCDF CL
but has been modified to recognize DAP URLs when they are presented in
place of local file names. The OPeNDAP netCDF CL has exactly the same
functional interface as the standard netCDF library available from
Unidata. That makes it a very powerful tool because it is possible to
tap into a large number of existing programs and build OPeNDAP clients.
Ferret, GRrADS and IDV are complex programs which have been
OPeNDAP-enabled using the netCDF CL.

Using the netCDF CL is not without its caveats. First netCDF does not do
a good job of representing the entire OPeNDAP data model (That's not a
dig at netCDF, it's a reflection of different design goals for two
pieces of software). It's very hard to access point data using the
netCDF CL, although we're working on this problem. Second, the C++ DAP
library is used to build the C version of the netCDF
CL\\<sup>\[writing_client_12.html#id1 1\]</sup> which means that the
application programs *must* be linked use a C++ compiler. Finally, it
can be tricky to build a shared object (aka DLL) using the C++ DAP
library.<sup>\[writing_client_12.html#id2 2\]</sup> If you're going to
do that, please contact us through user support or the technical
discussion list.

However, while the previous points are true, the principle distinction
between using the netCDF CL and one of the DAP class libraries is that
with the netcDF CL you often do not have to write any new software at
all! If you choose to use one of the DAP libraries, you're going to have
to write some code.

If you plan to OPeNDAP-enable an existing application using the Java
toolkit, the application typically must provide a Java Virtual Machine
(VM). You may be able to side-step this if you feel you can rely on the
host OS to provide a JVM and/or your target program already has a JVM
embedded in it. Still you must be sure there is a way to communicate
data values between the DAP library and the application.

To use the C++ toolkit you should insure that your client application
can be built with, or link with libraries constructed using
C++compilers. Check the current list of supported architectures on the
web-site, or contact the DODS technical support (<support@opendap.org>),
or write to the [OPeNDAP Mailing
Lists](http://www.opendap.org/mailLists/) mailing list for information
and/or help.

This tutorial will focus on using the C++ toolkit. In Section~2 the main
programmatic differences between the two class libraries is listed. Both
toolkits are essentially the same, with only minor differences between
them.

## Client Architecture

In essence, an OPeNDAP-enabled client uses overloaded URLs to form the
requests to a remote data server. Through the URL, the client connects
to the remote server and issues one of several requests. In response to
each request, the server will return a well-defined response that the
client can use to intern the structure and content of the remote data
into local data structures, as well as retrieve any attributes
associated with the remote data.

The C++ and Java toolkits share the same characteristics, though the
names of the objects and their methods may be slightly different. If you
understand how the clients are built, it will be easy to see how your
own client application can be OPeNDAP-enabled with minimal effort.

# The DAP Architecture

The DAP can be thought of as a layered protocol composed of MIME, HTTP,
basic objects, and complex, presentation-style, responses.

## The DAP uses HTTP which in turn uses MIME

Clients use HTTP when they make requests of DAP servers. HTTP is a
fairly straightforward protocol [for general information on HTTP see
www.w3.org/Protocols/](http://www.w3.org/Protocols/), and for the
HTTP/1.1 specification, [see
www.w3.org/Protocols/rfc2616/rfc2616.html](http://www.w3.org/Protocols/rfc2616/rfc2616.html).
It uses pseudo-MIME documents to encapsulate both the request sent from
client to server and the response sent back. This is important for the
DAP because the DAP uses headers in both the request and response
documents to transfer information. However, for a programmer who intends
to write a DAP client, exactly what gets written into those headers and
how it gets written is not important. Both the C++ and Java class
libraries will handle these tasks for you (look at the
[ResponseBuilder](http://www.opendap.org/api/pref/html/index.html) to
see how). It's important to know about, however, because if you decide
not to use the libraries, or the parts that automate generating the
correct MIME documents, then your server will have to generate the
correct headers itself.

## The DAP defines three objects

To transfer information from servers to clients, the DAP uses three
objects. Whenever a client asks a server for information, it does so by
requesting one of these three objects (note: this is not strictly true,
but the whole truth will be told in just a bit. For now, assume it's
true). The objects are the Dataset Descriptor Structure (DDS), Dataset
Attribute Structure (DAS), and Data object (DataDDS). These are
described in considerable detail in other documentation. The
Programmer's Guide contains a description of the [DDS and DAS
objects](http://docs.opendap.org/index.php/ProgrammerGuideChapter1).
These objects contain the name and types of the variables in a dataset,
along with any attributes (name-value pairs) bound to the variables. The
DataDDS contains data values. We have implemented the SDKs so that the
DataDDS is a subclass of the DDS object that adds the capacity to store
values with each variable.

| DatasetURL                                                                                        | DASType                                                                 | DDSType                                                                 |
|---------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------|-------------------------------------------------------------------------|
| [COADS Climatology](http://test.opendap.org/opendap/data/nc/coads_climatology.nc.html)            | [DAS](http://test.opendap.org/opendap/data/nc/coads_climatology.nc.das) | [DDS](http://test.opendap.org/opendap/data/nc/coads_climatology.nc.dds) |
| [NASA Scatterometer Data](http://test.opendap.org/opendap/data/hdf/S2000415.HDF.ascii?Wind_Speed) | [DAS](http://test.opendap.org/opendap/data/hdf/S2000415.HDF.das)        | [DDS](http://test.opendap.org/opendap/data/hdf/S2000415.HDF.dds)        |
| [Catalog of AVHRR Files](http://test.opendap.org/opendap/data/ff/1998-6-avhrr.dat.html)           | [DAS](http://test.opendap.org/opendap//ff/1998-6-avhrr.dat.das)         | [DDS](http://test.opendap.org/opendap/data/ff/1998-6-avhrr.dat.dds)     |

The DAP models all datasets as collections of
[variables](http://docs.opendap.org/index.php/ProgrammerGuideChapter1).
TheDDS and DataDDS objects are containers for those variables.

## The DAP also defines services

*Information of the DAP services is presented here for completeness and
because using these can help speed and simplify development of you
client. For example, you can use the HTML and ASCII services to look at
a data source using only a web browser. Similarly, the INFO response can
be used to look at the attributes and variables in a given data source.*

In the previous section we said that the DAP defined three objects and
all interaction with the server involved those three objects. In fact,
the DAP also defines other responses. They are:

| Object  | Response                                                                                                    |
|---------|-------------------------------------------------------------------------------------------------------------|
| ASCII   | Data can be requested in CSV form.                                                                          |
| HTML    | Each server can return an HTML form that facilitates building URLs.                                         |
| INFO    | Each server can combine the DDS and DAS and present that as HTML.                                           |
| VERSION | Each server must be able to respond to a request for it's version and the version of the DAP it implements. |
| HELP    | Each server must be able to provide a rudimentary help response.                                            |

In each case the server's response to these requests is built using one
or more of the basic three objects. Here are some links to various
datasets' ASCII, HTML and INFO responses:

| DataseProvider          | DatasetASCIIURL:                                                                             | HTML:                                                                     | INFO:                                                                     |
|-------------------------|----------------------------------------------------------------------------------------------|---------------------------------------------------------------------------|---------------------------------------------------------------------------|
| COADS Climatology       | [SST](http://test.opendap.org/opendap/data/nc/coads_climatology.nc.asc?SST)                  | [HTML](http://test.opendap.org/opendap/data/nc/coads_climatology.nc.html) | [INFO](http://test.opendap.org/opendap/data/nc/coads_climatology.nc.info) |
| NASA Scatterometer Data | [WindSpeed](http://test.opendap.org/opendap/data/hdf/S2000415.HDF.ascii?Wind_Speed)          | [HTML](http://test.opendap.org/opendap/data/hdf/S2000415.HDF.html)        | [INFO](http://test.opendap.org/opendap/data/hdf/S2000415.HDF.info)        |
| Catalog of AVHRR Files  | [DATE](http://test.opendap.org/opendap/data/ff/1998-6-avhrr.dat.ascii?year,day_num,DODS_URL) | [HTML](http://test.opendap.org/opendap/data/ff/1998-6-avhrr.dat.html)     | [INFO](http://test.opendap.org/opendap/data/ff/1998-6-avhrr.dat.info)     |

## Connecting to the server

To manage the connection between the client application and the remote
server, the DAP uses two objects. The **Connect** class manages one
connection to either a remote data server, or a local access. The
**Connections** class is used to manage a set of instances to the class
**Connect.** For each data source that the client opens, there must be
exactly one instance of the **Connect** class. The [C++ API Reference
documentation](http://www.opendap.org/api/pref/html/index.html) provides
a description for the C++ class' usage.

# Getting ready to write your client

An OPeNDAP-enabled client application creates a connection to the remote
server using the **Connect** class, and then issues requests to the
remote server through the **Connect** class methods. Please refer to the
[Geturl.java](http://scm.opendap.org/trac/browser/tags/Java-OPeNDAP/0.0.10/src/opendap/util/geturl/Geturl.java)
and
[geturl.cc](http://scm.opendap.org/trac/browser/tags/libdap/3.11.4/getdap.cc)
sources as examples of command-line based DAP client applications
written in Java and C++, respectively. Most of the software is
boilerplate. The following are sections of the Geturl.java client
application, later a description of the same example in C++ will be
provided. Again, please refer to the complete source listings referenced
above.

``` Java

 DConnect url = null;
 try {
    url = new DConnect(nextURL, accept_deflate);
 }
```

This code snippet instantiates a new instance of the **DConnect** class,
passing the URL referencing the remote data server, and a boolean flag
indicating that the client can accept responses from the server which
are compressed.

``` java

 if (get_data) {
    if ((cexpr==false) && (nextURL.indexOf('?') == -1)) {
        System.err.println("Must supply a constraint expression with -D.");
        continue;
    }
    for (int j=0; j < times; j++) {
        try {
           StatusUI ui = null;
           if (gui)
             ui = new StatusWindow(nextURL);
           DataDDS dds = url.getData(expr, ui);
           processData(url, dds, verbose, dump_data, accept_deflate);
        }
        catch (DODSException e) {
          System.err.println(e);
          System.exit(1);
        }
        catch (java.io.FileNotFoundException e) {
          System.err.println(e)
          System.exit(1);
        }
        catch (Exception e) {
          System.err.println(e);
          e.printStackTrace();
          System.exit(1);
        }
    }
 }

```

This compound statement block initiaties a data request to the remote
server. The **DConnect** method **getData** forms the data request to
the remote server by appending the string, `{expr}`, containing the
constraint-expression, onto the URL used in creating the initial
**DConnect** to the remote site. The parameter, `{ui}`, provides an
optional **StatusWindow** object to provide the status of the current
request to the client.

``` Java

 catch (DODSException e) {
   System.err.println(e);
   System.exit(1);
 }
 catch (java.io.FileNotFoundException e) {
   System.err.println(e);
   System.exit(1);
 }
 catch (Exception e) {
   System.err.println(e);
   e.printStackTrace();
   System.exit(1);
 }

```

Completing the try block is a series of catch blocks that catch
exceptions thrown by the DAP library code, and Java input/output and
general exceptions.

The C++ toolkit provides similar functionality as the Java toolkit
though the parameters to the individual **Connect** methods may vary.
The C++ client application
\[\[geturl.cc.html\]\[geturl.cc\|geturl.cc.html\]\[geturl.cc\]\] uses
the similar C++ toolkit classes as the Java toolkit to implement the
Geturl client application:

``` Cpp

  string name = argv[i];
  Connect url(name, trace, accept_deflate);

```

This code fragment declares an instance of the **Connect** class,
passing the URL referencing the remote data server, and a boolean flag
indicating that the client can accept responses from the server that are
compressed.

``` Cpp

else if (get_data) {
    if (expr.empty() && name.find('?') == string::npos)
       expr = "";
   for (int j = 0; j < times; ++j) {
       DataDDS dds;
       try {
           DBG(cerr << "URL: " << url->URL(false) << endl);
           DBG(cerr << "CE: " << expr << endl);
           url->request_data(dds, expr);
           if (verbose)
               fprintf( stderr, "Server version: %s\n"  url->get_version().c_str() ) ;
           print_data(dds, print_rows);
       }
       catch (Error &e) {
           e.display_message();
           throw;
       }
    }
}

```

This compound statement block initiaties a data request from the remote
server. The **Connect** method `{request_data}` forms the data request
to the remote server by appending the string, `{expr}`, containing the
constraint-expression, onto the URL used in creating the initial
**Connect** to the remote site.

``` Cpp

catch (Error &e) {
    e.display_message();
    continue
}

```

Completing the try block is a catch block that picks up exceptions
thrown by the DAP library code. The DAP C++ library throws several types
of exceptions, the most common of which are **Error** and
**InternalErr.** All of the exceptions are either instances of **Error**
or are specializations of it, so catching just **Error** will get
everything.

# Subclassing the data types

The DAP defines a data type hierarchy as the core of its data model.
This collection of data types includes scalar, vector and constructor
types. Most of the types are available in all modern programming
languages with the exceptions being **Url**, **Sequence** and **Grid.**
In the DAP library, the class **BaseType** is the root of the data type
tree.

## A quick review of the data types supported by the DAP

The DAP supports the common scalar data types such as Byte, 16- and
32-bit signed and unsigned integers, and 32- and 64-bit floating point
numbers. The DAP also supports Strings and Urls as basic scalar types.
The DAP includes Arrays of unlimited size and dimensionality. The DAP
also supports three type-constructors: **Structure**, **Sequence** and
**Grid**. A **Structure** on the DAP mimics a struct in C. A
**Sequence** is a table-like data structure inherited from the JGOFS
data system. It can be used to hold information that might be stored in
relational databases or tables, either flat or hierarchical. The JGOFS,
FreeForm and HDF servers all use the **Sequence** data type. Lastly, the
**Grid** data type is used to bind an array to a group of *map vectors*
, single dimension arrays that provide non-integral values for the
indexes of the array. The most typical use of a Grid is to provide
latitude and longitude registration for some georeferenced array data
(e.g., a projected satellite image). The DAP does not have a pointer
data type, but in some cases the **Url** data type can be used as a
pointer to variables between files. More information about the [DAP's
data type
hierarchy](http://docs.opendap.org/index.php/ProgrammerGuideChapter3) is
given in the Programmer's Guide.

## Creating the subclasses

When you build a DAP client, you may want to create a collection of data
type subclasses. In older versions of the libraries, this was a
requirement, but with newer versions this is no longer needed. If you
only want to read binary data from a server, you probably do not need to
make a set of datatype subclasses. One of the reasons why you might want
to subclass the types in libdap/Java-OPeNDAP are to provide specialized
processing operations that vary by data type only. For an example of
this, take a look at how the ASCII out put handler is organized – the
same design ideas apply to building clients.

If you do decide to write a set f subclasses, first make a factory class
for your subclasses. Take a look at the BaseTypeFactory class to see an
example of how this should be written. The factory class should define a
set of New\<<type>\> methods, one for each class you want to subclass.
If you only need subclasses for a few of the datatype classes - maybe
only Grid and Array - you still must provide *New* methods in the
factory class for all of the datatype classes, but must will reference
the classes already in libdap.

From BaseTypeFactory.h"

``` Cpp
class BaseTypeFactory
{
public:
    BaseTypeFactory()
    {}
    virtual ~BaseTypeFactory()
    {}

    virtual Byte *NewByte(const string &n = "") const;
    virtual Int16 *NewInt16(const string &n = "") const;
    virtual UInt16 *NewUInt16(const string &n = "") const;
    virtual Int32 *NewInt32(const string &n = "") const;
    virtual UInt32 *NewUInt32(const string &n = "") const;
    virtual Float32 *NewFloat32(const string &n = "") const;
    virtual Float64 *NewFloat64(const string &n = "") const;

    virtual Str *NewStr(const string &n = "") const;
    virtual Url *NewUrl(const string &n = "") const;

    virtual Array *NewArray(const string &n = "", BaseType *v = 0) const;
    virtual Structure *NewStructure(const string &n = "") const;
    virtual Sequence *NewSequence(const string &n = "") const;
    virtual Grid *NewGrid(const string &n = "") const;
};
```

Sample method definition from BaseTypeFactory.cc:

``` Cpp
Byte *
BaseTypeFactory::NewByte(const string &n) const
{
    return new Byte(n);
}
```

You must also define two methods in each of you subclasses:

1.  Your subclass must include a constructor that takes a single string
    argument (the name of the variable to be made) and that constructor
    must call the parent class' constructor with that argument; and
2.  You must include a definition of *ptr_duplicate()*. This method is
    more commonly called *clone()*.

Here are examples:

``` Cpp
ClientByte::ClientByte(const string &name) : Byte(name)
{
    // Build the instance of Client byte here;
}

BaseType *ClientByte::ptr_duplicate()
{
    return new ClientByte(*this);
}
```

Why are these needed?

The factory class enables the libdap parsers to build instances of your
specializations of the datatype classes without it requiring any
modification. It can use the factory's methods (NewByte, ..., NewGrid)
and be certain that it will always be instantiating the correct objects.

The ptr_duplicate() method is needed because occasionally, in the DAP
library, objects are declared with pointers specified as *BaseType \**.
If the *new* operator was used to copy such an object, the copied object
would be an instance of BaseType (the static type of the object) not the
type of the thing referenced (the dynamic type) . By using the
*ptr_duplicate()* method the DAP library is sure that when it copies an
object, it's getting an instance of the subclass defined by your server.
Note that the factory class make a new object with a given name, while
ptr_duplicate() copies an existing object returning a pointer to that
object. Also note that the type returned by the factory is specific to
the class being instantiated while ptr_duplicate() returns a pointer to
a (semi-) generic object.

Aside: Unlike the case where you are subclassing the DAP variable
classes to build a server, there's no need to implement *read()* when
building a client. The classes contain a default implementation of
*read()* that throws *InternalErr* if it is ever called (which no simple
client should ever do).

# Accessing the DDS object

The Data Descriptor Structure (DDS) is a data structure used by the DODS
software to describe datasets and subsets of those datasets. The DDS may
be thought of as the declarations for the data structures that will hold
data requested by some DODS client. Part of the job of a DODS server is
to build a suitable DDS for a specific dataset and to send it to the
client. Depending on the data access API in use, this may involve
reading part of the dataset and inferring the DDS. Other APIs may
require the server simply to read some ancillary data file with the DDS
in it.

For the client, the DDS object includes methods for reading the
persistent form of the object sent from a server. This includes parsing
the ASCII or XML representation of the object and, possibly, reading
data received from a server into a data object. The DDS object also
includes methods to access the variables it contains; those variables
provide ways to read data values out and store them in memory for use by
your client.

Note that the class DDS is used to instantiate both DDS and DataDDS
objects. A DDS that is 'empty' (its variables contain no actual data) is
used by servers to send structural information to the client. The same
DDS can be treated as a DataDDS when data values are bound to the
variables it defines.

Lastly, while DAP separates variables and attributes into two responses
(DDS and DAS) and provide objects in libdap for each of those responses,
it is possible to merge the attribute information (in the DAS) into the
DDS. This makes it much easier to perform operations on the variables in
the DDS when those operations depend on the values of attributes of
those variables.

For a complete description of the DDS layout and protocol, please refer
to [<cite>The OPENDAP User
Guide</cite>](http://docs.opendap.org/index.php/UserGuide) and [<cite>
The DODS Programmer's
Guide</cite>](http://docs.opendap.org/index.php/ProgrammerGuide) .

## Background on the persistent representations

The DDS has an ASCII representation (other than XML), which is
transmitted from a DODS server to a client. Here is the DDS
representation of a fictitious dataset containing a time series of
worldwide grids of sea surface temperatures:

``` Cpp
Dataset {
    Grid {
      ARRAY:
        Int32 sst[time = 404][lat = 180][lon = 360];
      MAPS:
        Float64 time[time = 404];
        Float64 lat[lat = 180];
        Float64 lon[lon = 360];
    } SST;
} weekly;
```

If the data request to this dataset includes a constraint expression,
the corresponding DDS might be different. For example, if the request
was only for northern hemisphere data at a specific time, the above DDS
might be modified to appear like this:

``` Cpp
Dataset {
    Grid {
      ARRAY:
        Int32 sst[time = 1][lat = 90][lon = 360];
      MAPS:
        Float64 time[time = 1];
        Float64 lat[lat = 90];
        Float64 lon[lon = 360];
    } sst;
} weekly;
```

The constraint has narrowed the area of interest; the range of latitude
values has been halved and there is only one time value in the returned
array.

See [<cite>The OPENDAP User
Guide</cite>](http://docs.opendap.org/index.php/UserGuide) and [<cite>
The DODS Programmer's
Guide</cite>](http://docs.opendap.org/index.php/ProgrammerGuide)\] for
descriptions of the DODS data types.

Reading data from a DDS object is the heart of writing your own OPeNDAP
client. To integrate the information contained in the DDS, you must do
two things. First you must decide how the data type hierarchy that is
part of the DAP can be represented in your client application. Some
client applications cannot represent all possible DAP data types
directly. Where possible the client developer should strive to support
as many data types as possible to facilitate access to the wide variety
of data accessible through OPeNDAP servers. In practice, once you know
how to map variables from the DAP into your client application, writing
code to build the DDS instance is easy.

``` Cpp

// url, an instance of Connect, was allocated here

else if (get_dds) {
     for (int j = 0; j < times; ++j) {
         DDS dds;
         try {
             url->request_dds(dds);
         }
         catch (Error &e) {
             e.display_message();
             delete url; url = 0;
             continue;   // Goto the next URL or exit the loop.
        }

        if (verbose)
            cerr << "Server version:  " << url->get_version().c_str() ) << endl;

        cerr << "DDS:" <<endl;
        dds.print(stdout);
    }
}
```

Above, the **Connect** method **request_dds** is called, passing a
reference to a DDS object. Following is an example from the C++ Matlab
client illustrates a simple traversal of the DDS object returned from
the **connect::request_data** method.

``` Cpp
static void
process_data(Connect &url, DDS &dds)
{
    if (verbose)
        cerr << "Server version: " << url.server_version() << endl;
    for (DDS::Vars_iter i = dds.var_begin(); i != dds.var_end(); i++) {
        BaseType *v = *i ;
        v->print_decl(cout, "", true);
        smart_newline(cout, v->type());
    }
}
```

In the C++ DAP classes, STL iterators are used to iterate over the
members (i.e., variables) in the DDS object. The iterator ***'i***
references pointers to the top-level **BaseType** objects held by the
DDS. The [<cite>The libdap Reference
Guide</cite>](http://www.opendap.org/api/pref/html/index.html) and
[<cite> The DODS Programmer's
Guide</cite>](http://docs.opendap.org/index.php/ProgrammerGuide)
provides a description for the each of the data types, and the methods
available to operate on them.

# Accessing the DAS object

The Data Attribute Structure (DAS) is a set of name-value pairs used to
describe the data in a particular dataset. The name-value pairs are
called the *attributes* . The values may be of any of the DODS simple
data types ( **Byte, Int16, UInt16, Int32, UInt32, Float32, Float64,
String and URL**), and may be scalar or vector. (Note that all values
are actually stored as string data.)

A value may also consist of a set of other name-value pairs. This makes
it possible to nest collections of attributes, giving rise to a
hierarchy of attributes. The DAP uses this structure to provide
information about variables in a dataset.

In the following example of a DAS, several of the attribute collections
have names corresponding to the names of variables in a hypothetical
dataset. The attributes in that collection are said to belong to that
variable. For example, the **lat** variable has an attribute units of
**degrees_north**.

``` Cpp
 Attributes {
    GLOBAL {
       String title "Reynolds Optimum Interpolation (OI) SST";
    }
    lat {
       String units "degrees_north";
       String long_name "Latitude";
       Float64 actual_range 89.5, -89.5;
    }
    lon {
       String units "degrees_east";
       String long_name "Longitude";
       Float64 actual_range 0.5, 359.5;
    }
    time {
       String units "days since 1-1-1 00:00:00";
       String long_name "Time";
       Float64 actual_range 726468., 729289.;
       String delta_t "0000-00-07 00:00:00";
    }
    sst {
       String long_name "Weekly Means of Sea Surface Temperature";
       Float64 actual_range -1.8, 35.09;
       String units "degC";
       Float64 add_offset 0.;
       Float64 scale_factor 0.0099999998;
       Int32 missing_value 32767;
    }
}
```

Attributes may have arbitrary names, although in most datasets it is
important to choose these names so a reader will know what they
describe. In the above example, the GLOBAL attribute provides
information about the entire dataset.

Data attribute information is an important part of the the data provided
to a DODS client by a server, and the DAS is how this data is packaged
for sending (and how it is received).

An example of Attribute handling in a client application is provided in
the **www-int** C++ source:

``` Cpp
void
LoaddodsProcessing::print_attr_table(AttrTable &at, ostream &os)
{
    for (AttrTable::Attr_iter i = at.attr_begin(); i != at.attr_end(); ++i) {
        int attr_num = at.get_attr_num(i);
        switch (at.get_attr_type(i)) {
          case Attr_container: {
              AttrTable *cont_atp = at.get_attr_table(i);
              os << "Structure" << endl << names.lookup(at.get_name(i), translate)
                 << " " << cont_atp->get_size() << endl;
              print_attr_table(*cont_atp, os);
              break;
          }
          case Attr_string:
          case Attr_url:
            if (attr_num == 1) {
                os << "String" << endl << names.lookup(at.get_name(i), translate) << endl
                   << at.get_attr(i) << endl;
            }
            else {
                os << "Array" << endl << "String " << names.lookup(at.get_name(i), translate)
                   << " 1" << endl << attr_num << endl;
                for (int j = 0; j < attr_num; ++j)
                    os << at.get_attr(i, j) << endl;
                os << endl;
            }
            break;

          // The remainder of this method's code has been elided. To see the
          // complete method, look at the source file
          // DODS/src/clients/ml-cmdln/LoaddodsProcessing.cc
        }
    }
}
```

As with the DDS, the DAS object is a container and STL iterators are
used to access its members (the attributes). There are several
differences, however, between the two containers. The DDS holds complete
objects, each of which is an instance of the class *BaseType.* A DAS,
however, holds a collection of attributes. Unless the attribute is
itself a container for other attribute type-name-value tuples, there is
no contained object to access with methods to run. Instead the DAS and
AttrTable classes themselves provide methods that are used to access the
type, name and value of the attributes. These accessor methods take as
their arguments a STL iterator.

For example, in the first case, the method
**AttrTable::get_attr_table()** is used to get a pointer to an
**AttrTable** (which is the \`value' of a container attribute). In the
second case the **AttrTable::get_name()** and **AttrTable::get_attr()**
methods are used to get the name and value of simple attributes. In each
case the **Attr_iter i** is passed to the methods.

> Note that newer versions of libdap provide a way to merge the
> information in the DAS object/response into the DDS object. See
> **DDS::transfer_attributes()**. Using this method, you software can
> use the DDS and BaseType object navigation methods and access a
> variable's *semantic* metadata by reading its **AttrTable** object. To
> read the individual attributes, use the methods defined for AttrTable
> on the variable AttrTable. This provides a more reliable way to
> navigate the the semantic metadata.

# Getting Data: Accessing the DataDDS object

Up till now we have talked about access to a data source's metadata. The
DDS provides access to the syntactic meta data and the DAS provides
semantic meta data (although note, as described above, it is possible to
merge these two and access both kinds of metadata from a single DDS
object). Use the *DataDDS* to access data values held by the data
source.

The *DataDDS* class is an extension of class *DDS* which contains the
binary data values returned by the remote server. It supports the same
methods as the class *DDS*, but the **BaseType::buf2val()** and
**BaseType::value()** methods can be used to extract data held in the
variable instances of *BaseType*. Use the *DDS*, *BaseType* and their
iterators to access the variables.

Use *Connect* to ask a remote server for a *DataDDS.* The *Connect*
provides **Connect::request_data()** to get the *DataDDS.* The
**request_data()** method accepts a constraint expression which can be
used to restrict the data returned by the remote server. See
\[<http://docs.opendap.org/index.php/UserGuideOPeNDAPMessages><cite>The
OPeNDAP User Guide</cite>\] for more information about constraint
expression syntax.

Use iterators to traverse the *DataDDS* and access the individual DAP
data variable objects. The following example prints the DAP data objects
declaration, to access the binary data returned by the server the DAP
provides access methods to retrieve the data object's buffer contents.

``` Cpp
static void
process_data(Connect &url, DDS *dds)
{
    if (verbose)
        cerr << "Server version: " << url.server_version() << endl;
    for (DDS::Vars_iter i = dds.var_begin(); i != dds.var_end(); i++) {
        BaseType *v = *i ;
        v->print_decl(cout, "", true);
        smart_newline(cout, v->type());
    }
}
```

The binary data returned by the server is stored in the **_buf** member
of each of the DAP's atomic data types. To retrieve the atomic data
type's buffer contents the **BaseType::buf2val** method is used.

``` Cpp
double *localVar = new double;
n_bytes = dds->var(q)->buf2val((void **)&localVar);
```

or using the type-safe **BaseType::value()** methods:

``` Cpp
double localBar
n_bytes = dds->var(q)->value(&localVar);
```

> The **value()** method is type-safe and thus less likely to errant use
> than **buf2val()**. The latter method was included in BaseType because
> it is sometimes necessary to access values in code that cannot easily
> determine the type of the values contained (but which is called by
> code that *can*). In general, always use the *value()* methods.
>
> Tip: To read data from an array variable without using *new* to
> allocate dynamic memory, use C++'s *vector<T>* class and pass the
> address of zero<sup>th</sup> element. This helps prevent accidental
> memory leaks.

The following C++ example is from the C++ Matlab client application and
illustrates the use of the C++ DAP classes and methods to access
elements from a Sequence data type.

``` Cpp
void
ClientSequence::print_one_row(ostream &os, int row, string space, bool print_row_num)
{
    const int elements = element_count();
    for (int j = 0; j < elements; ++j) {
        BaseType *bt_ptr = var_value(row, j);
        if (bt_ptr) {           // data
            bt_ptr->print_val(os, space, true);
        }
    }
}
void
ClientSequence::print_val_by_rows(ostream &os, string space, bool print_decl_p,  bool print_row_numners)
{
    const int rows = number_of_rows();
    for (int i = 0; i < rows; ++i) {
        print_one_row(os, i, space, false);
    }
}

```

The C++ client uses rows and columns to access the individual elements
of the *Sequence.* The C++ Matlab client uses two methods to accomplish
the extraction, the first, **print_val_by_row()** determines the number
of rows in the *Sequence* and calls the *print_one_row()* for each of
the rows in the *Sequence.* The C++ DAP implementation of the *Sequence*
data type provides the **var_value(row,col)** method to access the
individual elements of the *Sequence.* The **var_value()** method
returns a *BaseType* pointer to the row, column element of the
*Sequence.* To access the binary data value stored in that element, the
*BaseType* method **value()** can be used. The preceding example simply
prints the contents of the element, most client applications would
assign the contents to a local variable in the workspace.

``` Cpp
void
ClientArray::print_val(ostream &os, string, bool print_decl_p)
{
    if (print_decl_p) {
        os << type_name() << endl << var()->type_name() << " "
            << get_matlab_name() << " " << dimensions(true)
            << endl;
        // Write the actual dimension sizes on a separate line.
        for (Pix p = first_dim(); p; next_dim(p))
            os << dimension_size(p, true) << " ";
        os << endl;
    }
    for (int i = 0; i < length(); ++i)
        var(i)->print_val(os, "", false);
}

```

# Notes

Here's a collection of information that might be important to specific
clients but is hard to fit into a general tutorial.

- Because there are no meta data requirements to serve data via the
  OPeNDAP protocol, client applications may not find all the information
  they require to make use of the data. Many DAP servers support NcML as
  a way to alter or augment these attributes, although it is primarily a
  server-based tool.
- It is the client application's responsibility to provide the initial
  base URLs to the remote server site. There are discovery services
  available for DAP servers, but they do not integrate well into the
  access software stack.
- The DODS Project has developed two tools to help with serving datasets
  that contain many files. The first is to set up a \`file server' a
  kind of catalog of URLs that is itself a DODS data set. The second is
  NcML which provides, in addition to attribute modification, the
  ability to aggregate several data files so they appear as a single
  logical data set.
- You can get help from the \[<http://www.opendap.org/><cite>OPeNDAP
  Home Page</cite>\], the [<cite>OPeNDAP Mailing
  Lists</cite>](http://www.opendap.org/support/maillists) and the
  OPeNDAP user support (support@opendap.org).

# How the Java DAP library differs

Enumerations instead of iterators

DAP core with separate client and server specializations via Java
interfaces

Java versus C++ ;-)