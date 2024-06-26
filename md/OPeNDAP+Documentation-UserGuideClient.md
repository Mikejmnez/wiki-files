[return to User Guide](UserGuide "wikilink")

# The OPeNDAP Client

The OPeNDAP client is the program that sends a message to an OPeNDAP
server in order to get some data, or other information.

An OPeNDAP client is usually just a data analysis application program
modified to become a web browser, somewhat like any other web browser
with which you may be familiar. A web browser can only display the data
it receives, however. What makes an OPeNDAP client different from
another web browser is that once the data has been received from an
OPeNDAP server, the OPeNDAP client application can compute with it.

Like a web browser, an OPeNDAP client accepts a URL from a user, and
sends a message to that address, asking for the information specified in
the the URL. Unlike a typical web browser, an OPeNDAP client will not
know what to do with data returned for a web page containing text and
pictures, but an OPeNDAP server will return scientific data that an
OPeNDAP client can understand and process.

There is a wide range of OPeNDAP clients available, and it should not be
hard to find one you can use.

In fact, though it can become clumsy for advanced applications, you can
use an ordinary web browser as a client to most OPeNDAP servers, making
use of the server's [WWW
interface](UserGuideOPeNDAPMessages#WWW_Interface_Service "wikilink").
The [Quick Start Guide](QuickStart "wikilink") contains many examples of
exactly this.

After a basic web browser, the simplest clients to use are likely to be
the programs you're already using. If you use one of the popular data
analysis environments like Matlab or IDL, you can find a client command
you can incorporate into your environment to let you call OPeNDAP data
directly into your working data. If you use one of the netCDF-based
packages, like GrADS or Ferret, you can get a network-enabled version of
the program that will work with OPeNDAP URLs just as well as file names.

If none of those options work for you, there is a whole range of client
libraries you can use to develop a client of your own. Several of these
are supported by the OPeNDAP project, and there are others out there in
the world supported by other groups.

This page provides a list and very brief overview of the various
options, along with pointers to places you can find more information
about each one.

## Clients

OPeNDAP clients come in a variety of forms. The simplest are web
browsers, who use the OPeNDAP [WWW
interface](UserGuideOPeNDAPMessages#WWW_Interface_Service "wikilink")
and the [ASCII
response]([[UserGuideOPeNDAPMessages#ASCII_Service "wikilink") to check
out data sets and download data.

Beyond these, there are three categories of client. The first contains
clients you can use in conjunction with one of the popular data analysis
environments, the second is a collection of command-line clients useful
for scripting as well as testing, and the third contains a set of API
libraries you can use for developing your own client, or for converting
an existing body of code into an OPeNDAP client. These are reviewed in
that order below:

### Matlab, IDL, Ferret, GrADS

To use OPeNDAP with Matlab or IDL, you'll need the client for each. This
is a special program that issues a request for data from an OPeNDAP
server, and imports it into the environment. Links to [the Matlab
client](http://opendap.org/download/ml-structs.html) and [the IDL
client](http://opendap.org/download/idl-client.html) can be found on the
\[http;//opendap.org/download/index.html OPeNDAP software download
page\].

Using OPeNDAP data with the [GrADS](http://www.iges.org/grads) or
[Ferret](http://ferret.wrc.noaa.gov/Ferret) packages is even easier.
Because these packages are based on the netCDF library, and because that
library now supports reading OPeNDAP data sets, these packages can read
OPeNDAP URLs as easily as they read local files.

Special note. If you're using the Matlab client, and using it for
oceanographic data, you may be interested in the graphical user
interface available for it. See \[<http://oceanographicdata.org/toolbox>
Matlab OPeNDAP Ocean Toolbox\].

### Testing

There are a couple of command-line clients out there you can use, though
most people only use them for testing. Part of the libdap distribution
(the C++ interface) is a program called getdap, which takes an OPeNDAP
URL as a command-line argument and returns the reply to standard output.
This is typically used to check that the libdap C++ library is properly
compiled, but you can also use it to retrieve data.

Part of the OPeNDAP C library is a command-line client called octest.
This allows you to type commands to manipulate responses to an OPeNDAP
URL. Like the C++ test program, this can be construed as a test of the
library or a test of the servers, but it can also be used as a
command-line client, perhaps as an aid to automation.

Similar programs are part of the netCDF distribution. The ncdump program
outputs a "dump" of a netCDF file, and ncview provides a
better-formatted look at such a file. Since the standard netCDF library
can be linked to the OPeNDAP libraries, both these programs can be
readily aquired in their OPeNDAP-enabled form.

Here is a simple example, using the ncview program. This program simply
prints out the contents of a netCDF formatted data file, specified on
the command line, like this:

    > ncdump fnocl.nc

Using OPeNDAP, this same function may be executed from any computer
connected to the Internet by substituting a URL for the filename above:

    > ncdump http://dods.gso.uri.edu/cgi-bin/nc/data/fnocl.nc

Aside from the fact that the data is remote, and must be specified with
a URL, the program will seem to function in the same way it had with the
simple netCDF library (albeit somewhat more slowly due to having to make
network connections instead of local file operations).

    netcdf fnocl {
    dimensions:
        time_a = 16
        lat = 17 ;
        lon = 21 ;
        time = 16 ;

    variables:
        long u(time_a, lat, ion) ;
            u:units = ``meter per second'' ;
            u:long_name = ``Vector wind eastward component'' ;
            u:missing_value = ``-32767'' ;
            u:scale_factor = ``0.005'' ;
        long v(time_a, lat, ion) ;
            v:units = ``meter per second'' ;
            v:long_name = ``Vector wind northward component'' ;
            v:missing_value = ``-32767'' ;
            v:scale_factor = ``0.005'' ;
        double lat(lat) ;
            lat:units = ``degree North'' ;
        double lon(lon) ;
            lon:units = ``degree East'' ;
        double time(time) ;
            time:units = ``hours from base_time'' ;

    // global attributes:
            :base_time = ``88- 10-00:00:00'' ;
            :title = ``FNOC UV wind components
                               from 1988- 10 to 1988- 13.'' ;
    data:
     u =
      -1728, -2449, -3099, -3585, -3254, -2406, -1252,
        662, 2483, 2910, 2819, 2946, 2745, 2734,
      2931, 2601, 2139, 1845, 1754, 1897, 1854, -1686,
    ...

### Client Libraries

Several libraries exist that you can link with other software to create
an OPeNDAP client. Some of these are provided by the OPeNDAP project
itself, and some are projects of other groups.

The OPeNDAP libraries are functional equivalents of each other. They are
derived from separate code bases, but they do the same thing. They are
provided in different languages for the convenience of the implementer.

#### C++ Client Library

The [C++ library](http://opendap.org/download/libdap++.html), also
called libdap, was the original client implementation of the OPeNDAP
protocol. It provides classes to manage the connection between a client
and a data source, as well as classes for each of the data types, and
the other information (such as DAS and DDS) a client will encounter.

To use the library, you will need to provide implementations for some
abstract classes. Consult the [libdap
Overview](libdap_Overview "wikilink") for an introduction to the basic
concepts behind the use of this library. You will also find the [C++
library Reference](http://www.opendap.org/api/pref/html/index.html)
useful.

#### C Client Library

The OPeNDAP group supports a [C
library](http://opendap.org/download/oc.html). The C library is in many
ways a simpler library to use than the C++ libdap, but it is not as
flexible in other ways. Using the library is straightforward, and you'll
find a file called octutorial.html in the software release that provides
a detailed example of its use.

#### Java Client Library

The OPeNDAP group supports a [Java
implementation](http://opendap.org/download/java-dap.html) of the DAP.
On the Java page, there are links to download the Java class
documentation.

#### netCDF API Library

The [netCDF library](http://www.unidata.ucar.edu/software/netcdf)
deserves special note. This is a drop-in replacement for the standard
netCDF library. (In fact, as of release 4.0, it *is* the standard netCDF
library.) This means that converting a program that depends on the
netCDF API to use OPeNDAP is as simple as re-linking with an updated
version of the netCDF library.

See the [netCDF home page](http://www.unidata.ucar.edu/software/netcdf)
for information about how to use that library.

#### Python library

[Pydap](http://pydap.org) is an implementation of the OPeNDAP client in
pure Python. This is tremendously useful for scripting complicated
applications with lots of download steps. This is not supported by the
OPeNDAP group, so please refer to the [Pydap site](http://pydap.org) for
more information about it.