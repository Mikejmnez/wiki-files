# What is OPeNDAP?

OPeNDAP provides a way for researchers to access scientific data
anywhere on the Internet from a wide variety of new *and existing*
programs. By developing network versions of commonly used data access
libraries, such as
[<cite>NetCDF</cite>](http://www.unidata.ucar.edu/packages/netcdf/guide.txn_toc.html)
,
[<cite>HDF</cite>](http://www.ncsa.uiuc.edu/SDG/Software/HDF/HDFIntro.html)
, [<cite>JGOFS</cite>](http://www1.whoi.edu/jgofs.html) , and others,
the OPeNDAP project can capitalize on years of development of data
analysis and display packages that use those file formats, allowing
users to continue to use programs with which they are already familiar.

The OPeNDAP architecture uses a client/server model, with a *client*
that sends requests for data out onto the network to some *server*, that
answers with the requested data. This is exactly the model used by the
[World Wide Web](http://www.w3.org/hypertext/WWW/TheProject.html) where
client programs called browsers submit requests to web servers for the
data that make up web pages. Of course, OPeNDAP clients can do much more
than browse this data. Using flexible data types suitable for many uses,
including scientific data, the OPeNDAP servers deliver real data
directly to the client program in the format needed by that client.

The network communication model used by OPeNDAP uses URL addresses and
web servers to deliver data to the researcher. This is done by using the
OPeNDAP software to convert a researcher's data analysis software into a
sophisticated (though specialized) web browser. In addition to providing
network-compatible versions of popular data access APIs, the OPeNDAP
project also provides a software client and server toolkit to help other
developers create network-compatible OPeNDAP versions of other APIs.

To expand the universe of data available to a user, OPeNDAP incorporates
a powerful data translation facility, so that data may be stored in data
structures and formats defined by the data provider, but accessed by the
user in a manner identical to the access of local data files on the
user's own system. Though there are limitations on the types of data
that may be translated (See [Section
6.1.2](Wiki_Testing/OPeNDAPUserGuide6 "wikilink")), the facility is
flexible and general enough to handle many of the possible translations.
There are two important results:

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
developed by oceanographers, its application is not constrained to
oceanographic data. The organizing principles and algorithms may be
applied to many other fields where data can be stored on computers.

The uniformity with which data appears makes the system very useful both
for easing data analysis for a researcher, but also for automating data
transport and manipulation tasks. OPeNDAP libraries make data seem
uniform, and by making the data analysis programs network-aware,
simplify scripting and automation. For example, NOAA's [Live Access
Server (LAS)](http://ferret.pmel.noaa.gov/Ferret/LAS/home/) (see, for
example, [My NASA Data](http://mynasadata.larc.nasa.gov/data.html)) uses
OPeNDAP, as do many of the real-time observing systems that make up the
[Integrated Ocean Observing System (IOOS)](http://www.ioos.gov), like
[Gulf of Maine Ocean Observing System](http://gomoos.org).

The population of people who may be interested in a system such as
OPeNDAP may be divided into data consumers and data providers. Though it
was an important observation to the development of OPeNDAP that the two
roles are often assumed by the same scientists, the division is a useful
one for the introduction of the system. The following two sections
provide a broad introduction to the roles of data consumer and data
provider. The remainder of this guide is organized around this
distinction between classes of users.

### The OPeNDAP Client

OPeNDAP uses a client/server model. The OPeNDAP servers are web servers
equipped to interpret an OPeNDAP URL sent to them. (See [Chapter
5](Wiki_Testing/OPeNDAPUserGuide5 "wikilink")) The OPeNDAP client
program can be any program that uses one of the supported APIs, such as
JGOFS or netCDF.([3](Wiki_Testing/OPeNDAPUserGuideFootNotes "wikilink"))

Without OPeNDAP, an application program that uses one of the common data
access APIs such as netCDF will operate as shown in [the figure
below](:Image:unlinked.gif "wikilink"). The user makes a request for
data from the application program. The program in turn uses procedures
defined by the data access API to access the data, which is stored
locally on the host machine. Some APIs are somewhat more sophisticated
than this, of course, but their general operation is as simple, and the
whole process happens on a single machine.

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
above](:Image:unlinked.gif "wikilink") has been linked with an OPeNDAP
version of the data access API library. Now, in addition to being able
to use local data as before, the application program is able to access
data from OPeNDAP servers anywhere on the Internet in exactly the same
manner as the local data.

To make some analysis program into an OPeNDAP client, just re-link it
with the OPeNDAP implementation of the supported API library. This is a
simple process, generally requiring only a few minutes. This will create
a program that accepts URLs as well as file pathnames to identify data
to be read. (See [Section
3.1](Wiki_Testing/OPeNDAPUserGuide3 "wikilink")).

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
client translates the data into the data model it understands. (See
[Chapter 6](Wiki_Testing/OPeNDAPUserGuide6 "wikilink") for more
information about the OPeNDAP data model.) Because the data transmitted
from an OPeNDAP server to the client travel in the OPeNDAP format, the
dataset's original storage format is completely irrelevant to the
client. If the client was originally designed to read netCDF format
files, the data returned by the OPeNDAP-netCDF library will appear to
have been read from a netCDF file, whatever the actual format of the
files from which the data were
read([4](Wiki_Testing/OPeNDAPUserGuideFootNotes "wikilink")). If the
program expects JGOFS data, the OPeNDAP-JGOFS library will return data
that seem to have come from a JGOFS dataset, and so on.

OPeNDAP does not pretend to remove all the overhead of data searches. A
user will still have to keep track of the URLs of interesting data sets
in the same way a user must now keep track of the names of files
containing interesting data. (You may run across datasets where the data
consists of OPeNDAP URLs. These are the OPeNDAP file servers, and have
been developed by OPeNDAP users to organize datasets consisting of large
numbers of individual files.)

## Providing Data with OPeNDAP

The OPeNDAP data provider is the person or organization willing to make
their digital datasets available to the community with an OPeNDAP
server.

The OPeNDAP designers recognize that many data users are also data
providers, and the software was built with a recognition that providing
the data should be as simple and as straightforward as possible. In many
cases, once a local web server is equipped to become an OPeNDAP server,
a scientist need do very little beyond what must be done simply to make
the data available locally. (i.e., Put the data into a file format that
can be read by the locally used data analysis and display programs.) The
tasks of a data provider can be separated into three parts:

- Install and configure the OPeNDAP server.([Section
  5.2](Wiki_Testing/OPeNDAPUserGuide5 "wikilink"))
- Store the data in the appropriate file format and store it where the
  server can find it.
- Create whatever ancillary data files are needed by the data set (if
  any).

### The OPeNDAP Server

The OPeNDAP data server is made up of two pieces. You can think of them
as a front-end and a back-end. Generally speaking, they will run on the
same machine, and from a user's perspective, appear to be a single
server.

The front-end server is a Tomcat servlet, and is also called the
**OPeNDAP Lightweight Front-End Servlet** (OLFS). Its job is to receive
your request for data and manage all the different forms such a request
might take. For example, you might be asking for the data, an ASCII
version of the data, or a reply to a SOAP message. The front-end server
can also reply to THREDDS catalog requests, for information about the
data.

The **Back-End Server** (BES) is more directly about performance, and is
designed to respond quickly and efficiently to requests from the OLFS.
It is a pure data server, and has only one format of request and
response, relying on the OLFS to convert formats to accommodate the
user. Most users won't make requests directly to the BES.

<figure>
<img src="HyraxArchitecture.jpg" title="HyraxArchitecture.jpg" />
<figcaption>HyraxArchitecture.jpg</figcaption>
</figure>

*Hyrax* is an alternative name for the OPeNDAP 4 Data Server.

See [Section 6.2](Wiki_Testing/OPeNDAPUserGuide6 "wikilink") for a
description of the data returned by these requests and see [Section
2.1](Wiki_Testing/OPeNDAPUserGuide2 "wikilink") for a description of the
OPeNDAP URL syntax used to send these requests.

See [the OPeNDAP 4 Data Server documentation](Hyrax "wikilink") for a
description of how to install and configure an OPeNDAP data server.

### Administration and Centralization of Data

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