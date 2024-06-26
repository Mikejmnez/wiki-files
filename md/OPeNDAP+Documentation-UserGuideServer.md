[Back to User Guide Contents](UserGuide "wikilink")

# The OPeNDAP Server

Any server that responds to the messages described in [OPeNDAP
Messages](UserGuideOPeNDAPMessages "wikilink") using the [OPeNDAP Data
Model](UserGuideDataModel "wikilink") is an OPeNDAP server. The messages
and the data model are published standards, available to anyone to use.

The OPeNDAP group, in addition to publishing and maintaining the
standards described here, maintains a server that complies with the
standard. This is [Hyrax](Hyrax "wikilink").

## Hyrax

The OPeNDAP group produces and supports [Hyrax](Hyrax "wikilink"), a
modular data server that can be used in a variety of ways to serve a
wide variety of data with OPeNDAP.

Hyrax is flexible enough that it can serve data that currently exist in
a wide variety of data storage formats, ranging from HDF4 data files to
netCDF data to data in some arbitrary non-standard format using the
FreeForm module.

Hyrax is modular, too, which means it can be outfitted to serve the
particular needs of your data without weighing down its performance with
features you don't need.

### How Hyrax Works

The [Hyrax](Hyrax "wikilink") server is actually a combination of two
distinct servers, running on the same machine, or on two machines
connected with a very fast link. One server serves as the user-facing
"front end" to the system, while the other provides the "engine room",
optimized to turn requests around quickly.

The front end server is called the **O**PeNDAP **L**ightweight **F**ront
end **S**erver (OLFS). It receives requests for data and services in
multiple formats and forms, and is meant to be as "user friendly" as a
server can be. It also handles such chores as authentication and
authorization-checking, and responding to catalog and bot requests. It
can also construct complex data requests from multiple requests from its
parner server.

<figure>
<img src="HyraxArchitecture.jpg" title="HyraxArchitecture.jpg" />
<figcaption>HyraxArchitecture.jpg</figcaption>
</figure>

This other server, called the **B**ack **E**nd **S**erver (BES), does
only one thing—provide data—and is designed to do it fast. It handles
the compute-intensive parts of processing an OPeNDAP client request for
data. This results in higher performance for small requests while not
penalizing the larger requests.

Separating the two roles allows several paths to optimizing a server,
while still resulting in higher performance for small requests. An OLFS
controlling multiple BES processes on a machine can process multiple
requests quicker because while one BES is occupied with retrieving its
data, another can be processing its data. An OLFS controlling BES
processes on multiple machines can implement a rudimentary
load-balancing scheme to protect servers from overload. What's more,
compute-intensive clients can optimize their processes further by
sending requests directly to the BES.

### Installing Hyrax

The two parts of [Hyrax](Hyrax "wikilink") are both installed by the
same script, but they are two different animals, which may require
different kinds of attention from an administrator. Testing OLFS
requires a working BES, so it's typical to start the post-unpacking
process with the BES.

#### Installing BES

The BES is a standalone server. Its installation involves parking its
code somewhere to be run as a daemon and configuring your system to
execute it.

After that, though, there are several configuration options you'll need
to address by installing software on your machine, and by editing the
[Hyrax configuration file](Hyrax_-_BES_Configuration "wikilink"), called
bes.xml. Details of these options are in the [Hyrax](Hyrax "wikilink")
documentation, but these are the decisions you'll have to make:

What kind of data
Hyrax is equipped to serve data stored in any of several different file
formats, HDF, netCDF and others. Each different format requires its own
handler, and your machine must have shared libraries containing these
handlers installed. For example, in order to serve netCDF data, the
netCDF handler library must be available to Hyrax, and identified in its
[configuration file](Hyrax_-_BES_Configuration "wikilink"). The handlers
are shared libraries and are installed separately. Find instructions
under [Loading
Handlers](Hyrax_-_BES_Configuration#Loading_Handlers "wikilink").

<!-- -->

Where the data lives
A server equipped to serve data must be able to find that data. Lines in
the bes.xml file identify the disk location of your data files. Hyrax
can provide a limited amount of browsing capacity to users, through its
[WWW
interface](UserGuideOPeNDAPMessages#WWW_Interface_Service "wikilink").
Access is specified by identifying directories where data resides. See
[Pointing_to_data](Hyrax_-_BES_Configuration "wikilink").

<!-- -->

What kinds of services
Along with simply supplying data, Hyrax can supply several different
services, including [serving the data as ASCII
values](UserGuideOPeNDAPMessages#ASCII_Service "wikilink"), and the
form-driven [WWW
interface](UserGuideOPeNDAPMessages#WWW_Interface_Service "wikilink").
The bes.xml file is used to specify the list of services. The drivers
for these services are installed as part of the default installation of
Hyrax, but they need to be chosen and identified in bes.xml. See
instructions at [Loading
Handlers](Hyrax_-_BES_Configuration#Loading_Handlers "wikilink").

Once these three tasks are accomplished, and once data is moved into the
appropriate directories, your server should be ready to provide data to
all comers, but most specifically to its OLFS.

##### The FreeForm Module

One of the shared libraries to use for serving data is called the
FreeForm module. This module allows you to serve data in fairly
arbitrary formats by writing a format description file to sit alongside
your data file. If you have data that isn't in one of the supported file
formats, consider writing format descriptions using FreeForm, and
serving the data that way.

See the [FreeForm documentation](FreeForm "wikilink") for more
information.

#### Installing OLFS

The OLFS is a Java servlet, so its installation involves installing the
Tomcat servlet infrastructure, and then configuring that with the OLFS.
There are four configuration files, but unless yours is an unusual case,
you'll likely only have to look at two of them. See
[Hyrax_-_OLFS_Configuration](Hyrax_-_OLFS_Configuration "wikilink")
for more information.

Use the
[olfs.xml](Hyrax_-_OLFS_Configuration#olfs.xml_Configuration_File "wikilink")
file to specify these important features of the OLFS operation:

Address of BES
The OLFS is the front door to data from the BES. Use the olfs.xml file
to identify where to find this server. See the instructions for the
[BESManager](Hyrax_-_OLFS_Configuration#BESManager "wikilink").

<!-- -->

Dispatch handlers
The OLFS "offers" the incoming URL to a series of "dispatch handlers"
until one accepts it and executes. This allows the OLFS to offer
directory and catalog services, as well as data services and more. Use
the olfs.xml file to nominate the dispatch handlers (they come with the
OLFS, and do not need to be separately installed). See [handler
instructions](Hyrax_-_OLFS_Configuration#HTTP_GET_Handlers "wikilink")
for more.

<!-- -->

Catalog requests
The OLFS can handle [THREDDS](Hyrax_-_THREDDS_Configuration "wikilink")
catalog requests, as well as directory requests, an older part of the
DAP standard that also involves data about a collection of data files.
The THREDDS information can be static (provided from a file) or dynamic
(generated by a review of the available data).

<!-- -->

File access
You may have individual files you want served intact via this server.
These might include documentation of the data files, or of the server.
See [File Dispatch
Handler](Hyrax_-_OLFS_Configuration#File_Dispatch_Handler "wikilink")
instructions.

There are some other features, too, like a bot blocker and a version
message, as well as the experimental SOAP message handler. These are
also configured with
[olfs.xml](Hyrax_-_OLFS_Configuration#olfs.xml_Configuration_File "wikilink"),
and you'll find more information about them at the link.

The other configuration file you might need to edit is
[catalog.xml](http://www.unidata.ucar.edu/projects/THREDDS/tech/TDS.html),
briefly reviewed under [THREDDS](#THREDDS "wikilink"), below.

#### THREDDS

[THREDDS](http://www.unidata.ucar.edu/projects/THREDDS/tech/TDS.html) is
a catalog standard for scientific data promoted by scientists at
[UCAR](http://www.unidata.ucar.edu). It allows

The default file will allow dynamic catalogs to be created and should
allow your server to respond to THREDDS requests properly. It's possible
you may want to serve a static THREDDS catalog, too. This can be done by
editing the catalog.xml file, and you'll find links to instructions for
that at [Hyrax - THREDDS
Configuration](Hyrax_-_THREDDS_Configuration "wikilink").

#### Multiple BES Configuration

You can configure a single OLFS to work with multiple BES instances.
This offers an opportunity to do rudimentary load balancing, or to
isolate data on one server or another. See the [Hyrax configuration
chapter](Hyrax_-_Configuring_The_OLFS_To_Work_With_Multiple_BES's "wikilink")
for more information on the subject.