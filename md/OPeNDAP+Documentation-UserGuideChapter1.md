# What is OPeNDAP?

The OPeNDAP provides a way for ocean researchers to access oceanographic
data anywhere on the Internet from a wide variety of new *and existing*
programs. By developing network versions of commonly used data access
Application Program Interface (API) libraries, such as
[<cite>NetCDF</cite>](http://www.unidata.ucar.edu/packages/netcdf/guide.txn_toc.html)
,
[<cite>HDF</cite>](http://www.ncsa.uiuc.edu/SDG/Software/HDF/HDFIntro.html)
, [<cite>JGOFS</cite>](http://www1.whoi.edu/jgofs.html) , and others,
the OPeNDAP project can capitalize on years of development of data
analysis and display packages that use those APIs, allowing users to
continue to use programs with which they are already familiar.

The OPeNDAP architecture uses a client/server model, with a {\em

{client}} that sends requests for data out onto the network to some
"server", that answers with the requested data. This is exactly the
model used by the [<cite>World Wide
Web</cite>](http://www.w3.org/hypertext/WWW/TheProject.html) where
client programs called browsers submit requests to web servers for the
data that make up web pages. Of course, OPeNDAP clients can do much more
than browse this data. Using flexible data types suitable for many uses,
including scientific data, the OPeNDAP servers deliver real data
directly to the client program in the format needed by that client.

In fact, the network communication model used by OPeNDAP uses URL
addresses and web servers ("httpd") to deliver data to the researcher.
This is done by using the OPeNDAP software to convert a researcher's
data analysis software into a sophisticated (though specialized) web
browser. In addition to providing network-compatible versions of popular
data access APIs, the OPeNDAP project also provides a software client
and server toolkit to help other developers create network-compatible
OPeNDAP versions of other APIs.

To expand the universe of data available to a user, OPeNDAP incorporates
a powerful data translation facility, so that data may be stored in data
structures and formats defined by the data provider, but may be accessed
by the user in a manner identical to the access of local data files on
the user's own system. Though there are limitations on the types of data
that may be translated (See ([<cite> data,trans</cite>](http://www))),
the facility is flexible and general enough to handle many of the
possible translation. There are two important results:

- A user may not need to know that data from one set are stored in a
  format different from data in another set. Further, it may be possible
  that "neither" data set is stored in a format readable by the original
  (i.e. without OPeNDAP) version of the data analysis and display
  program he or she uses.
- No segment of OPeNDAP users will be effectively cut off from accessing
  data because of its storage format. A scientist who wishes to make his
  or her data available to other OPeNDAP users may do so while keeping
  that data in what may actually be a highly idiosyncratic storage
  format. Of course, it doesn't have to be in a highly idiosyncratic
  format. The point is that OPeNDAP can handle a wide variety of
  possible cases.

The combination of the OPeNDAP network communication model and the data
translation facility make OPeNDAP a powerful tool for the retrieval,
sampling, and display of large distributed datasets. Though OPeNDAP was
developed by oceanographers, its application is not constrained to
oceanographic data. The organizing principles and algorithms may be
applied to many other fields where data can be stored on computers.

The population of people who may be interested in a system such as
OPeNDAP may be divided into data consumers and data providers. Though it
was an important observation to the development of OPeNDAP that the two
roles are often assumed by the same scientists, the division is a useful
one for the introduction of the system. The following two sections
provide a broad introduction to the roles of data consumer and data
provider. The remainder of this guide is organized around this
distinction between classes of users.

## Why Use OPeNDAP to Read Data?

A scientist wishing to examine and sample some dataset will typically be
comfortable using a relatively small number of data analysis and display
programs or packages. Some of these packages will use one of the popular
data access APIs currently available. However, few data access APIs
provide direct access to distributed data

refers to datasets that reside on different computers which are linked
by a network such as the Internet. The computers may or may not be
physically remote from each other. The main point is that the computers
manage their data resources independently. In this guide the terms
"remote\\} and {\em distributed\\" are used to imply independently
managed resources.}, so this access must be made with network tools,
such as web browsers or "ftp". While relatively straightforward in
principle, this process can nonetheless become time-consuming and
somewhat challenging in practice.

The following example illustrates some of the differences between
accessing distributed data with the tools currently in widespread use,
and the same operation using OPeNDAP.

### An Example: Using ftp

The advent of the WWW has made possible simple data browsers that allow
sophisticated interactive sampling of on-line datasets. Using a web
browser and "ftp", a user can sample any of several large oceanographic
datasets available on the Internet. However, there are several problems
with these data search engines that may only become apparent when a user
actually tries to use the data.

Among the problems that can arise are those that appear when a user
tries to use the results of one dataset to search a second dataset.
Suppose that a user wishes to choose a sea-surface temperature image
from the NOAA/NASA Pathfinder AVHRR archive at:

    http://podaac-www.jpl.nasa.gov/mcsst/mcsst_subset.html

using the results of a time-series generated from the COADS Climatology
archive at:

    http://ferret.wrc.noaa.gov/fbin/climate_server

The steps are theoretically straightforward:

1.  Create the time series from the COADS Climatology archive. This is
    done by answering the menu of options on the COADS web page.
2.  Import the time series from step 1 to the user's local data analysis
    system. Note that this step may itself require several steps:
    1.  The data must be down-loaded, using "ftp" or a similar program.
    2.  Once down-loaded, the data may have to be converted into a
        format that can be read by the data analysis program.


3.  Examine the data and formulate a request to the AVHRR archive. This
    is again done by answering the menu of option on the AVHRR Web page.
    Note that the COADS and AVHRR pages are not completely compatible in
    this respect. For example, the date formats of the two pages are
    different.
4.  Import the result of step 3 to the user's local data display system.
    This may also require several steps:
    1.  The data must be down-loaded again.
    2.  And again, once down-loaded, the data may have to be converted
        into a format that can be read by the data analysis program.
        Note that the set of available formats on the COADS page are
        distinct from the available options from the AVHRR archive.


5.  Think about the results.

Though the procedure is straightforward and the web servers designed to
make sampling the datasets a simple task, upon close examination, the
combination of the steps may create unforeseen difficulties. For
example, a request to the COADS server will return either a spreadsheet
suitable for use on a PC, a netCDF format file, or a file in one of a
selection of simple ASCII formats. If the user is fortunate, the
returned file will already be in a format compatible with the desired
analysis package. But not all users will be so fortunate. Often this
file must be converted to some other file format before it can be
imported to the user's analysis program. This may or may not be a simple
task.

Even a file format for which a user is properly equipped may be used in
an unfamiliar manner. For example, the independent and dependent
variables might be in a different order or an ASCII data file may use
tabs instead of spaces.

Assuming the import of the COADS data has been accomplished and
boundaries for the AVHRR search identified, the task of selecting from
the second archive may begin. Unfortunately, the request to the AVHRR
archive will return either a GIF picture, an HDF format file, or a raw
(binary) data file. Again, importing this output into the user's
analysis program may or may not be simple, but it will not be the same
procedure as the one used for the first data request.

Other problems are also apparent. The COADS Climatology sampling program
requests the user supply dates (month and day), whereas the AVHRR
archive asks for the "Julian day" (an integer between 1 and 365 or 366).
One server will accept "S" and "W" to indicate South latitudes and West
longitudes, while the other requires that these be indicated with
negative coordinate values. The sampling of the COADS dataset, while
flexible, may not allow sampling in the manner the user needs. It
cannot, for example, provide a section except along a line of constant
latitude or longitude. If a user wanted to see a section along a NE-SW
line, it would be a challenging and time-consuming task to assemble one
from many small data requests.

Further, it might be desirable to use the results of sampling these two
databases to construct a time series. This could conceivably mean
repeating the entire procedure many times.

### An Example: Using OPeNDAP

To produce the same data selection using OPeNDAP, a user would follow
essentially the same steps. However, the steps themselves would be
performed differently. Once the user's data analysis package has been
converted to an OPeNDAP client (([<cite>
opd-client,link</cite>](http://www))), the \tbd{add xref to install GUI

clients} accesses to the remote datasets are made through the analysis
package itself. Instead of specifying a data file by a pathname
reference to some local disk file, the user specifies a URL, which may
point to either a local or a remote dataset. Here is a re cap of the
same operation, outlined as they would be performed by an OPeNDAP
application program:

1.  Create the time series from the COADS Climatology archive. This is
    done by using the sampling facilities of whatever data analysis
    program a scientist is familiar with. If desired, OPeNDAP constraint
    expressions may be used to reduce the network load, or to provide a
    sampling scheme not supported by the data analysis program.
2.  The data need not be imported to the user's data analysis program,
    since it was down-loaded and converted automatically in step 1.
3.  Examine the data and formulate a request to the AVHRR archive. This
    is again done through the sampling facilities of whatever data
    analysis program the user is using, and OPeNDAP constraint
    expressions. Note that, whatever their actual format, both COADS and
    AVHRR archives appear to the OPeNDAP client to be stored in
    identical formats.
4.  The data need not be imported to the user's data analysis program,
    since it was down-loaded and converted automatically in step 3.
5.  Think about the results.

It is important to note that "any" data analysis package that can handle
one of the DODS-supported data access APIs can be converted into an
OPeNDAP client program capable of reading data stored by "all" of the
DODS-supported data access APIs. (There are some limitations on
translation. See ([<cite> intro,opd-client</cite>](http://www)) and
([<cite> data,trans</cite>](http://www)) for more information.)
Therefore, assuming the user has some analysis package capable of doing
the required sampling and analysis on local data, all the steps would be
performed from within that package, just as if the user were operating
on local files. The result is a simpler procedure, even though the same
essential steps are followed.

The OPeNDAP scenario has, among others, the following advantages:

- The user need not learn about any of the archival formats, since the
  OPeNDAP server and client cooperate to deliver the data in the format
  in which the analysis package expects to see it. Whereas the user of
  the ftp server has to worry about importing the data into the analysis
  program, the OPeNDAP client program imports it transparently.
- The user can sample the distant datasets in any fashion supported by
  his or her own (local) analysis package. Unnecessary data need not be
  sent over the Internet.
- By appending a "constraint expression" to the URLs given to the
  analysis program, the user can sample data using techniques that their
  analysis program *cannot* do.\footnote{For example, suppose a user
  wishes to access the NODC XBT database using a program that uses the
  netCDF API. A program that can process the arrays that netCDF
  manipulates are largely unsuitable for XBT station data. However, a
  user can define constraint expressions in the URL to sample the data
  and deliver it in a form the netCDF API can use. For more information
  about constraint expressions, see Section~(opd-client,constraint). For
  more information about data models and translation, see
  Chapter~(data).}\tbd{Use a different example in the footnote}
- A substantial amount of the searching and sampling is performed on the
  server machines. This reduces Internet traffic, as well as decreasing
  the load on the local machine.

### The OPeNDAP Client

OPeNDAP uses a client/server model. As mentioned, the OPeNDAP servers
are simply "httpd} web servers, equipped to interpret an OPeNDAP URL
sent to them. (See \chapterref{opd-server".) The OPeNDAP client program
can be any program that uses one of the supported APIs, such as JGOFS or
netCDF.\footnote{Or a program specially developed to read data from
OPeNDAP servers.}

Without OPeNDAP, an application program that uses one of the common data
access APIs such as netCDF will operate as shown in
![Image:intro,fig,unlinked](intro,fig,unlinked "Image:intro,fig,unlinked").
The user makes a request for data from the application program. The
program in turn uses procedures defined by the data access API to access
the data, which is stored locally on the host machine. Some APIs are
somewhat more sophisticated than this, of course, but their general
operation is similar to this outline.

\figureplace{The Architecture of a Data Analysis Package.}{htbp}
{intro,fig,unlinked}{unlinked.ps}{unlinked.gif}{}

The operation of an OPeNDAP client is illustrated in
![Image:intro,fig,linked](intro,fig,linked "Image:intro,fig,linked").
Here, the *same application program* that was used in
![Image:intro,fig,unlinked](intro,fig,unlinked "Image:intro,fig,unlinked")
has been linked with an OPeNDAP version of the data access API. Now, in
addition to being able to use local data as before, the application
program is able to access data from OPeNDAP server anywhere on the
Internet in the same manner as the local data.

To make some program into an OPeNDAP client, it must only be re-linked
with the OPeNDAP implementation of the supported API library. This is a
simple process, generally requiring only a few minutes. The process will
create a program that accepts URLs, specifying a location for the data
somewhere on the Internet, in addition to file pathnames which only
specify a location on the local platform's file system. (See ([<cite>
opd-client,link</cite>](http://www)).)

\figureplace{The Architecture of a Data Analysis Package Using
OPeNDAP.}{htbp} {intro,fig,linked}{linked.ps}{linked.gif}{}

OPeNDAP also provides a data translation facility. Data from the
original data file is translated by the OPeNDAP server into an OPeNDAP
data model for transmission to the client. Upon receiving the data, the
client translates the data into the data model it understands. (See
([<cite> data</cite>](http://www)) for more information about the
OPeNDAP data model.) Because the data transmitted from an OPeNDAP server
to the client travel in the OPeNDAP format, the data set's original
storage format is completely irrelevant to the user of an OPeNDAP
client. If the client was originally designed to read netCDF format
files, the data returned by the OPeNDAP-netCDF library will appear to
have been read from a netCDF file, whatever the actual format of the
files from which the data were read\footnote{Note that there is a limit
to what can be translated. An API meant to support two-dimensional
arrays may be able to handle one-dimensional vector data, but a program
designed to process one-dimensional vector data will not know what to do
with a two-dimensional array. The set of data access APIs supported by
OPeNDAP contain several such mismatches. See Section~(data,trans) for
more information.}. If the program expects JGOFS data, the DODS-JGOFS
library will return data that seem to have come from a JGOFS dataset,
again, no matter what the actual input file format.

OPeNDAP does not pretend to remove all the overhead of data searches. A
user will still have to keep track of the URLs of interesting data sets
in the same way a user must now keep track of the names of files
containing interesting data. an OPeNDAP \new{catalog service} is in the
process of being constructed that will help users scan the available
datasets.

## Providing Data with OPeNDAP

The OPeNDAP data provider is the person or organization willing to make
their digital datasets available to the community with an OPeNDAP
server.

The designers of OPeNDAP recognized that many of the data users are also
the data providers, and OPeNDAP was built with a recognition that
providing the data should be as simple and as straightforward as
possible. In many cases, once a local web server is equipped to become
an OPeNDAP server, a scientist need do very little beyond what must be
done simply to make the data available locally. (i.e., Put the data into
a file format that can be read by the locally used data analysis and
display programs.) The tasks of a data provider can be separated into
three parts:

- Install and configure the OPeNDAP server.

(([<cite> opd-server,install</cite>](http://www)).)

- Create whatever ancillary data files are needed by the data set (if
  any). (([<cite> intro,ancillary</cite>](http://www)).) %
- Register the data set with the master directory (optional). %
- Create the data catalog.

### The OPeNDAP Server

The OPeNDAP data server is simply made up of a regular
<font color='green'>httpd</font> server equipped with CGI programs (or
filters) that will respond to requests for dataset structure, data
attributes, and data itself. (See ([<cite> data,dap</cite>](http://www))
for a description of the data returned by these requests and see
([<cite> opd-client,url</cite>](http://www)) for a description of the
OPeNDAP URL syntax used to send these requests.) Most of the task of a
data provider consists of configuring this server. While perhaps not a
trivial task, it potentially represents far less effort than packaging a
dataset for submission to some central data archive. Furthermore,
modifying a server's configuration to accommodate new data will be an
almost trivial task, involving the simple editing of a configuration
file.

### Ancillary Data

In order for an OPeNDAP client to accept data from an OPeNDAP server, it
must be able to allocate the data structures and arrange internal labels
to organize the incoming data. The information the client library needs
to do this organizing is called the ancillary data\footnote{It is also
referred to as

the Data Descriptor Structure and the Data Attribute Structure. See

Chapter~(data) for more details about these structures.}. For many APIs,
the ancillary data is inherent in the data files themselves, and the
OPeNDAP server can glean that information by scanning the data files.
For large data archives, where scanning the data files is impractical,
and that might not change often, OPeNDAP can cache the ancillary data to
speed access times. When a client requests the ancillary data, the
OPeNDAP server can check this data cache first before scanning the data
files.

This feature is useful in other cases because not all data file formats
are self-describing. For example, a data set might contain several files
of time vs. temperature data; the header information describing which
numbers are temperature and which time may be in a different file or may
simply be understood by the user of the local data analysis program
equipped to look at this data. As an example, data accessed by OPeNDAP
servers using the FreeForm data access API require provider-created
ancillary data files.

### Administration and Centralization of Data

Under OPeNDAP, there is no central archive of data. Data under OPeNDAP
is organized in a manner similar to the World Wide Web itself. That is,
all one need do to make one's data available is to start up a properly
configured "httpd" server on an Internet node that has access to the
data to be served. Each data provider is free to join and to leave the
system when it is convenient, just as any proprietor of a web page is
free to delete it or add to it as whimsy demands.

Of course, as can also be seen on the World Wide Web, there are some
disadvantages to the lack of central authority. If no one knows about a
web site, no one will visit it. Similarly, listing a dataset in a
central data catalog, such as the Global Change Master Directory
([<cite><http://gcmd.gsfc.nasa.gov/></cite>](http://gcmd.gsfc.nasa.gov)),can
make data available to other researchers in a way that simply
configuring an OPeNDAP server does not. OPeNDAP provided a facility for
registering a data set with the GCMD catalog, which makes the data set
known to the OPeNDAP data location service.

The remainder of this book will be divided into three major sections:
instructions on the building and operating of OPeNDAP clients; a
tutorial and reference on running OPeNDAP servers and making data
available to OPeNDAP clients; and technical documentation describing the
implementation details (and the motivation behind many of the design
decisions) of the OPeNDAP software.