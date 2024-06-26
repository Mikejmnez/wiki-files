# The OPeNDAP Client

There are many different data analysis packages in use. Some packages,
such as MATLAB and IDL, are commercially available, but many more are
written for a specialized need or application. Many of these use one of
the widely available sets of scientific data access functions (called an
Application Program Interface, or API ) such as NetCDF, JGOFS, or HDF.
There is great variety among all these programs, but one feature they
share is that they all access data through files containing that data8.
That is to say that each program begins by identifying a file containing
the data the user wishes to examine or analyze.

An OPeNDAP client is simply a data analysis application linked with the
OPeNDAP libraries instead of the standard data access API. Using this
program, a user can look at files containing data in the same way as was
possible without the OPeNDAP libraries. However, by using these
libraries, a user can also use a URL (URL) , instead of a simple file
name, to specify data located anywhere on the Internet. Figure 1.1.3 and
figure 1.1.3 illustrate the operation of an application program linked
with a standard data access API, and the same program linked with the
OPeNDAP version of that API.

An OPeNDAP client is then a data analysis application program modified
to become a web browser, somewhat like any other web browser (NCSA
Mosaic, Netscape Navigator) with which you may be familiar. A web
browser can only display the data it receives, however. What makes an
OPeNDAP client different from another web browser is that, unlike
Netscape, once the data has been received from an OPeNDAP server, the
OPeNDAP client application can compute with it.

Like a web browser, an OPeNDAP client accepts a URL from a user, and
parses it to come up with a protocol, an address, and a message. (See
Section 2.1 for more information about URLs.) The browser then sends a
message to the address, directed to the server who can service the
desired protocol, asking for the information specified in the remainder
of the URL. Unlike a typical web browser, an OPeNDAP client will not
know what to do with data returned for a web page containing text and
pictures, but an OPeNDAP server will return scientific data that an
OPeNDAP client can understand and process.

Here is a simple example, using the ncview program. This program simply
prints out the contents of a netCDF formatted data file, specified on
the command line, like this:

    > ncview fnocl.nc

Using OPeNDAP, this same function may be executed from any computer
connected to the Internet by substituting a URL for the filename above:

    > ncview http://dods.gso.uri.edu/cgi-bin/nc/data/fnocl.nc

(See figure 2.1 Aside from the fact that the data is remote, and must be
specified with a URL, the program will seem to function in the same way
it had with the simple netCDF library (albeit somewhat more slowly due
to having to make network connections instead of local file operations).
You can find dncview (the ncview program linked with the OPeNDAP
library) in the

    $DODS_ROOT/src/nc-dods/ncview

<font color="red">No. But you can get it off of the web</font>

directory. Running the above command will produce the following output:

<font color="red">This is ncdump output and ncdump is part of the netcdf
library. version 4.1 and later include DAP support</font>

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

Although there are packaged OPeNDAP browsing programs that a user can
use to look at data, the user can also construct his or her own. Linking
an OPeNDAP API with an already existing program allows a user to create
a customized web browser that can access data available from any OPeNDAP
server connected to the Internet.

The OPeNDAP APIs are designed to accurately mimic the behavior of
several different commonly used scientific data APIs. As of this writing
(August 25, 2004), the OPeNDAP API set includes:

<center>

| API            | Description                                                                                                                                                                                                                                                                                                                                         | Components                                                                                                                                                                                                                                       |
|----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| netCDF         | Support for gridded data, such as satellite data, interpolated ship station data, or current meter data.                                                                                                                                                                                                                                            | Server and client.                                                                                                                                                                                                                               |
| JGOFS          | Support for relational data, such as Sequences. Created by the Joint Globar Ocean Flux Study (JGOFS) project for use with oceanographic station data.                                                                                                                                                                                               | Server only.                                                                                                                                                                                                                                     |
| HDF            | Support for gridded data. Commonly used for astronomical data and model data.                                                                                                                                                                                                                                                                       | Server only.                                                                                                                                                                                                                                     |
| DSP            | Oceanographic and geophysical satellite data. Provides support for image processing. Developed at the University of Miami/RSMAS. Primarily used for AVHRR and CZCS data.                                                                                                                                                                            | Server only.                                                                                                                                                                                                                                     |
| GRIB           | Support for gridded binary data. GRIB is the World Meteorological Organization (WMO) format for the storage of weather information and the exchange of weather product messages.                                                                                                                                                                    | Server only, available using the TDS server.                                                                                                                                                                                                     |
| BUFR           | The WMO's standard set of codes for the transmission and storage of meteorological data, using a compressed format with each data value occupying the least number of bits necessary to contain its range of values. Suitable for meteorological observations made from a single point or set of points.                                            | Server only, available using the TDS server.                                                                                                                                                                                                     |
| FreeForm       | On-the-fly conversion of arbitrarily formatted data, including relational data and gridded data. May be used for sequence data, satellite data, model data, or any other data format that can be described in the flexible FreeForm format definition language. This server can be used to serve data stored in almost all home-grown data formats. | Server only.                                                                                                                                                                                                                                     |
| native OPeNDAP | The OPeNDAP class library may be used directly by a client program. It supports relational data, array data, gridded data, and a flexible assortment of data types that can be combined to accommodate most data models.                                                                                                                            | Client. We provide libraries for C, C++ and Java. The independent PyDAP project provides support of Python. Because the C API for DAP is a true API, it should be easy to build interfaces to other interpreted languages using the SWIG system. |

</center>

The API set is extensible, meaning that developers can use the OPeNDAP
software toolkit to write OPeNDAP-compliant versions of new APIs. See
The DODS Toolkit Programmer's Guide for more information.

The most important result of this architecture is that, just as the use
of the DAP-enabled ncview program above is identical to the original
ncview, a user can use remote OPeNDAP data and continue to use the same
data analysis and display programs with which he or she is familiar. Any
program that uses one of the OPeNDAP-supported APIs may be re-linked to
use the OPeNDAP version of that API. This creates an OPeNDAP client.
That and a connection to the Internet, are all that a researcher
requires to gain access to the available OPeNDAP data.

## Configuring Programs to Use OPeNDAP

In the past, relinking programs so that they could our netCDF API was
cumbersome. However, recently (2007-2010) using funding from the
National Science Foundation, we have migrated DAP support from our own
versionof the netCDF version 3.x API back into the netCDF library that
Unidata makes available. All you need to do to build software that uses
netCDF so that it can also read from DAP servers is to ensure your local
copy of the netCDF library was built with that option enabled. Most
builds OS/X and linux will have this as the default; win32 uses should
check the documentation that comes with the library.

<font color="red">

Old text... Mostly wrong, but ...

Relinking an existing program with the OPeNDAP implementation of some
data API is a simple procedure. Find the directory that contains the
source/object code of the program you want to re-link and modify the
makefile (typically called Makefile) for the program so that the
OPeNDAP-compliant API library is used in place of the standard API
library. (If you can't find the libraries on your system, see Appendix
A, or ask the system administrator.) These libraries are:

> libdap++.a Software common to all of the OPeNDAP-supported APIs. OPeNDAP also uses facilities from some standard libraries, and these must also be included in the link to resolve all the symbols.
> libwww.a The World Wide Web library. This contains the functions used to communicate between the OPeNDAP client and server.
> libexpect. Functions from the expect library are used to communicate between OPeNDAP client processes.
> libtcl.a Contains definitions necessary for the expect library. The use of this library in the link is not related to the use of Tcl by OPeNDAP clients.
> libstdc++.a The GNU C++ class library (This is not necessary if using g++ to re-link.)

You will also need to include the library containing the
OPeNDAP-compliant version of the API. The name of this library of course
depends on the API, but it is generally in the form

    lib API-dods.a

Where API is an abbreviation indicating the API emulated by the
specified library. For example, the OPeNDAP-compliant netCDF library is
called libnc-dods.a and the JGOFS version is libjg-dods.a.

### An Example Using netCDF

The ncview program is a simple utility that prints the contents of a
netCDF-format file to standard output. This section outlines the process
used to modify the ncview makefile to link that program with the OPeNDAP
netCDF API, thereby turning ncview into a network-ready OPeNDAP client.
The process of linking any other program with the corresponding OPeNDAP
library is entirely analogous to this one and only requires the
substitution of the program name and the appropriate library.

First the link flags were modified so that the library search path would
include the likely places to find the OPeNDAP libraries:

    LDFLAGS = -g -L$(DODS_ROOT)/lib

DODS_ROOT is an environment variable that indicates the root directory
of the OPeNDAP installation, and in this manual is used as shorthand for
this directory. It is typically called something like /usr/local/DODS.
If you cannot find these directories on your system, consult your system
administrator, or refer to Appendix A for information about acquiring
and installing the OPeNDAP software.

After the link flags were modified, the OPeNDAP libraries were added to
the list of libraries used. The order in which the libraries are listed
is important.

    LIBS = -lnc-dods -ldap++ -lnc-dods -ldap++ -lwww -ltcl
           -lexpect -lz -lrx

> NOTE: Because OPeNDAP is implemented as a core set of classes
> contained in one library (libdap++.a) and a set of specializations of
> those classes in a second library (libnc-dods.a), and because there is
> a circular dependence between those two libraries, they must be
> included twice in the linker command.

Finally, g++ was substituted for the link command
([9](Wiki_Testing/OPeNDAPUserGuideFootNotes "wikilink")).

</font>

### Potential Problems

When a user links an existing a program to the OPeNDAP libraries, there
are several possible conditions that may cause problems.

- Some programs use more than one API.
- Some programs access data using both API and UNIX system calls.
- Some programs use undocumented features of the APIs.

If this is the case for a given program, there is generally no good
solution beside rewriting the software to conform to a strict usage of
the data reading parts of the given API. Of course if the problem is
that the program uses more than one API, you can try linking the program
with an OPeNDAP-compliant version of the second API as well.

- Re-linked programs can be very large.

The OPeNDAP libraries are large, and the g++, www, expect, and tcl
libraries on which they are built are even larger. This means that the
executable version of a re-linked OPeNDAP client can seem unreasonably
obese. Much of the disk space is occupied by symbol tables, which can be
removed from the executable file with the strip utility. In many cases,
a user can recover a substantial amount of disk space this way.

> CAUTION: Without familiarity with the OPeNDAP software, it is best
> only to strip the executable files. Stripping object files or
> libraries might leave them in a useless condition for the linker.
> Furthermore, stripping an executable file removes symbol names, which
> may make diagnosing problems more difficult.

The OPeNDAP libraries only affect the data reading functionality of the
specified API. There are no OPeNDAP replacements for functions like
netCDF's ncputrec(), that write data to a disk file. These functions are
included in the OPeNDAP-compliant API library, but they operate in a
manner identical to the original (non-OPeNDAP) versions, that is, they
work on local files only, attempting to write "over the network" will
result in an error.

## Writing New OPeNDAP Programs

The OPeNDAP software may also be used to write new programs. This may be
done either through one of the OPeNDAP-supported API libraries, such as
netCDF or JGOFS, or by using the OPeNDAP data access protocol directly.
There are advantages and disadvantages to each approach.

The biggest advantage of writing new code using an OPeNDAP-supported API
such as netCDF or JGOFS is that the programmer in question is probably
already familiar with the use of that API. Writing an OPeNDAP program
using an adapted API is not significantly different than writing the
same program with the original API. While writing this new program, it
will be useful to remember that the data the program uses will often be
remote, implying that data retrieval may not be instantaneous, and that
implementation of local caching to store requested data might be a good
idea, but other than that, the process is the same as writing a program
using the regular API.

It is also possible to use the OPeNDAP data access protocol directly.
This is somewhat more involved than using one of the OPeNDAP-compliant
API libraries, and C++ is the only language supported for this. However,
this approach can provide substantially more efficient programs. For
further information about this approach, refer to the technical
information about the DAP in [<cite>The DODS Toolkit Programmer's
Guide</cite>](http://docs.opendap.org/index.php/ProgrammerGuide).