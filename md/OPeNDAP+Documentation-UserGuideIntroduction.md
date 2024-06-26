[Return to top level](UserGuide "wikilink")

# What is OPeNDAP?

OPeNDAP provides a way for researchers to access scientific data
anywhere on the Internet from a wide variety of new *and existing*
programs.

The OPeNDAP architecture uses a client/server model, with a *client*
that sends requests for data out onto the network to some *server*, that
answers with the requested data. This is exactly the model used by the
[World Wide Web](http://www.w3.org/hypertext/WWW/TheProject.html) where
client browsers submit requests to web servers for the data that make up
web pages. Of course, OPeNDAP clients can do much more than browse this
data. Using flexible data types suitable for many uses, including
scientific data, the OPeNDAP servers deliver real data directly to the
client program in the format needed by that client.

OPeNDAP clients are very specialized browsers, constructed by linking
legacy data analysis programs with OPeNDAP-enabled versions of the data
access APIs they use, or by modifying the programs to use one of the
OPeNDAP data access APIs. In addition to providing a sophisticated set
of network-compatible APIs in several languages, there are also
libraries of legacy APIs available in OPeNDAP-aware versions. The
popular
\[<http://www.unidata.ucar.edu/packages/netcdf/guide.txn_toc.html><cite>NetCDF</cite>\]
library, for example, can read data from remote OPeNDAP data sources as
easily as it reads from a local file.

To expand the universe of data available to a user, OPeNDAP incorporates
a powerful data translation facility, so that data may be stored in data
structures and formats defined by the data provider, but accessed by the
user in a manner identical to the access of local data files on the
user's own system. Though there are limitations on the types of data
that may be translated (see
[Translation](UserGuideDataModel#Translation "wikilink")), the facility
is flexible and general enough to handle many of the possible
translations. There are two important results:

- A user may not need to know that data from one set are stored in a
  format different from data in another set. Further, it may be possible
  that *neither* data set is stored in a format readable by the original
  version of the data analysis and display program he or she uses.

<!-- -->

- No segment of OPeNDAP users will be effectively cut off from accessing
  data because of its storage format. A scientist who wishes to make his
  or her data available to other OPeNDAP users may do so while keeping
  that data in what may actually be a highly idiosyncratic storage
  format. (Of course, it doesn't *have* to be in a highly idiosyncratic
  format.)

The combination of the OPeNDAP network communication model and the data
translation facility make OPeNDAP a powerful tool for the retrieval,
sampling, and display of large distributed datasets. Though OPeNDAP was
developed by oceanographers, its application is not limited to
oceanographic data. The organizing principles and algorithms may be
applied to many other fields.

The uniformity with which data appears that makes the system so useful
for data analysis also eases automating data transport and manipulation
tasks. For example, NOAA's
\[<http://ferret.pmel.noaa.gov/Ferret/LAS/home/>Live Access Server
(LAS)\] (see, for example, <http://mynasadata.larc.nasa.gov/data.html>)
uses OPeNDAP, as do many of the real-time observing systems that make up
the \[<http://www.ioos.gov>Integrated Ocean Observing System (IOOS)\],
like the \[<http://gomoos.org>Gulf of Maine Ocean Observing System
(GoMOOS)\].

The population of people who may be interested in a system such as
OPeNDAP may be divided into data consumers and data providers. Though it
was an important observation to the development of OPeNDAP that the two
roles are often assumed by the same scientists, the division is a useful
one for the introduction of the system. The following two sections
provide a broad introduction to the roles of data consumer and data
provider. The remainder of this guide is organized around this
distinction between classes of users.

# The OPeNDAP Client

OPeNDAP uses a client/server model. The OPeNDAP servers are web servers
equipped to interpret an OPeNDAP URL sent to them. An OPeNDAP client
composes and sends messages to those servers. There are standalone
OPeNDAP clients, but most clients are constructed from data analysis
programs modified to get their data remotely, from the internet, rather
than locally, from a file.

Without OPeNDAP, an application program that uses one of the common data
access APIs such as netCDF will operate as shown in [the figure
below](:Image:unlinked.gif "wikilink"). The user makes a request for
data from the application program. This program uses some data access
API to read and write data. The program uses procedures defined by that
API to access the data, which is typically stored on the same host
machine. Some APIs are somewhat more sophisticated than this, of course,
but their general operation is as simple.

<center>

<figure>
<img src="unlinked.png" title="actual size" />
<figcaption>actual size</figcaption>
</figure>

The Architecture of a Data Analysis Package

</center>

The operation of an OPeNDAP client is illustrated in the [figure
below](:Image:unlinked.gif "wikilink"). Here, the *same application
program* that was used in [ the figure
above](:Image:unlinked.gif "wikilink") has been modified to use one of
the OPeNDAP API libraries. Now, in addition to being able to use local
data as before, the application program is able to access data from
OPeNDAP servers anywhere on the Internet in the same manner as the local
data.

To make some analysis program into an OPeNDAP client, just re-link it
with the OPeNDAP API library. (Or, if your program uses one of the
supported legacy API libraries, like netCDF, you can link with the
OPeNDAP version of that library.) This will create a program that
accepts URLs as well as file pathnames to identify data to be read. (See
[the OPeNDAP Client](UserGuideClient "wikilink")).

<center>

<figure>
<img src="linked.png" title="actual size" />
<figcaption>actual size</figcaption>
</figure>

The Architecture of a Data Analysis Package Using OPeNDAP

</center>

OPeNDAP also provides a data translation facility. Data from the
original data file is translated by the OPeNDAP server into the OPeNDAP
data model for transmission to the client. Upon receiving the data, the
client translates the data into the [data
model](UserGuideDataModel "wikilink") it understands. Because the data
transmitted from an OPeNDAP server to the client travel in the OPeNDAP
format, the dataset's original storage format is completely irrelevant
to the client. If the client was originally designed to read netCDF
format files, the data returned by the OPeNDAP-netCDF library will
appear to have been read from a netCDF file, whatever the actual format
of the files from which the data were
read([4](Wiki_Testing/OPeNDAPUserGuideFootNotes "wikilink")). If the
program expects JGOFS data, the OPeNDAP-JGOFS library will return data
that seem to have come from a JGOFS dataset, and so on.

OPeNDAP does not pretend to remove all the overhead of data searches. A
user will still have to keep track of the URLs of interesting data in
the same way a user must now keep track of the names of files. (You may
run across datasets where the data consists of OPeNDAP URLs. These are
the OPeNDAP file servers, and have been developed by OPeNDAP users to
organize datasets consisting of large numbers of individual files.) But
it does make the access to that data simpler and quicker.

The OPeNDAP group provides a whole array of client software to implement
this communication standard. These range from standalone clients to
libraries to link with existing software. There is more about these in
[OPeNDAP Client](UserGuideOPeNDAPClient "wikilink").

# The OPeNDAP Services

The communication between an OPeNDAP client and server is specified by
the Data Access Protocol (DAP). This defines the range of messages a
server must understand and the kinds of replies it makes.

There are two categories of messages an OPeNDAP server can understand.
Some are required by the DAP, and others are merely suggested. A server
is considered to be DAP-compliant if it can respond intelligibly to the
required messages.

The requests messages a server is required to understand are these:

Data Description
Data values come in types and sizes. An array, for example, might
consist of 10 integers. The value "ten" and the type "integer" describe
the array. This request returns information about data types, so that a
receiving program can allocate space appropriately. See [Data
Description Structure (DDS)](UserGuideDataModel#DDS "wikilink").

<!-- -->

Data Attribute
This is a request to provide information *about* data, and typically
includes information like units, names of data types, reference
information and so on. See [Data Attribute Structure
(DAS)](UserGuideDataModel#DAS "wikilink").

<!-- -->

Data
The server also must be able to respond to a request for the data
itself. See [data response](UserGuideDataModel#DataDDS "wikilink").

A server may respond to requests like these, too:

ASCII
Some servers can convert data to ASCII values on the fly. This allows
users to view data using a standard web browser, assuming the data are
not too large. See [ASCII return](UserGuideServer#ASCII "wikilink").

<!-- -->

Info
The info response is a formatted page containing information from the
Data Attributes and Data Description responses. It's meant to be a
human-readable way to show what's available in a dataset via a standard
web browser. See [Info response](UserGuideServer#Info "wikilink").

<!-- -->

HTML
Very similar to the info response is the HTML response. This provides
not only the information from the info response, but also includes a
Javascript form to help you build a request for data from the same data
file. The best description of the HTML form is in the [Quick Start
Guide](QuickStart "wikilink").

<!-- -->

SOAP
OPeNDAP servers can provide their data in terms of a SOAP request and
response. For more information see [SOAP
Request](UserGuideDataModel#SOAP "wikilink").

<!-- -->

DDX
The DDX is an XML version of the Data Attribute and Data Description
replies. See [DDX Request](UserGuideServer#DDX "wikilink").

# The OPeNDAP Server

OPeNDAP provides a definition of the communication between client and
server. Servers and clients who conform to that standard can communicate
with each other. In addition to the OPeNDAP communication standard
itself, the OPeNDAP group also provides an implementation of that
standard server protocol, called *Hyrax.*

The OPeNDAP data server is made up of two pieces. You can think of them
as a front-end and a back-end, though a client will not be aware of the
separation. They will often be run on the same machine, and even when
they are not, a client will see only the front end.

The front-end server is a Tomcat servlet, and is also called the
**OPeNDAP Lightweight Front-End Servlet** (OLFS). Its job is to receive
your request for data and manage all the different forms such a request
might take. For example, you might be asking for the data, an ASCII
version of the data, or a reply to a SOAP message looking for data. The
front-end server can also reply to THREDDS catalog requests, for
information about the data, and can directly provide some information
about the data, too.

The **Back-End Server** (BES) is more strictly about performance, and is
designed to respond quickly and efficiently to requests from the OLFS.
It is a pure data server, and has only one format of request and
response, relying on the OLFS to convert messages to whatever format
will accommodate the user. Most users won't make requests directly to
the BES.

<figure>
<img src="HyraxArchitecture.jpg" title="HyraxArchitecture.jpg" />
<figcaption>HyraxArchitecture.jpg</figcaption>
</figure>

*Hyrax* is an alternative name for the OPeNDAP 4 Data Server.

See [Data Model](UserGuideDataModel "wikilink") for a description of the
data returned by these requests and see [OPeNDAP
Server](UserGuideServer "wikilink") for a description of the URL syntax
used to send these requests.

See [the OPeNDAP 4 Data Server documentation](Hyrax "wikilink") for a
description of how to install and configure an OPeNDAP data server
(Hyrax).

# Administration and Centralization of Data

Under OPeNDAP, there is no central archive of data. Data under OPeNDAP
is organized in a manner similar to the World Wide Web itself. That is,
all one need do to make one's data available is to start up a properly
configured server on an Internet node that has access to the data to be
served. Each data provider is free to join and to leave the system when
it is convenient, just as any proprietor of a web page is free to delete
it or add to it as whimsy demands.

Of course, as can also be seen on the World Wide Web, there are some
disadvantages to the lack of central authority. If no one knows about a
web site, no one will visit it. Similarly, listing a dataset in a
central data catalog, such as the Global Change Master Directory
([<cite><http://gcmd.gsfc.nasa.gov/></cite>](http://gcmd.gsfc.nasa.gov)),can
make data available to other researchers in a way that simply
configuring an OPeNDAP server does not. OPeNDAP provided a facility for
registering a data set with the GCMD catalog, which makes the data set
known to the OPeNDAP data location service. The THREDDS catalog service
is another way to make information about your data widely available.

The remainder of this book will be divided into three major sections:
instructions on the building and operating of OPeNDAP clients; a
tutorial and reference on running OPeNDAP servers and making data
available to OPeNDAP clients; and technical documentation describing the
implementation details (and the motivation behind many of the design
decisions) of the OPeNDAP software.