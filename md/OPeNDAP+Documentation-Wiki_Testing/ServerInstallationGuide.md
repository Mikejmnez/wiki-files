The OPeNDAP software provides a way to access data over the Internet,
from programs that weren't originally designed for that purpose, as well
as some that were. The OPeNDAP software implements the Data Access
Protocol
[DAP](Wiki_Testing/ServerInstallationGuideFootnotes "wikilink"), version
2. A DAP server is a program that sends data in a standard transmission
format to a client that has requested it. A DAP client is a program,
running on a networked computer somewhere in the world, that requests
and receives data. A client can be a specialized DAP client, or a
standard web browser.

Though originally developed to deal with oceanographic data, the OPeNDAP
software has found wide use outside that community, and is now used for
several different kinds of science data.

Nothing limits the use of the DAP to science data; its framework will
support many different data types. But the DAP has facilities for
accommodating large arrays, relational tables, irregular grids, and many
other otherwise anomalous data types. DAP servers can be adapted to
serve data from any kind of storage format, and versions that support
several popular data storage formats are readily available.

# The OPeNDAP Server

The OPeNDAP DAP server is just an ordinary WWW server (httpd) equipped
with a CGI program that enable it to respond to requests for data from
DAP client programs. Web servers and CGI programs are standard parts of
the Web, and the details of their operation and installation are beyond
the scope of this guide. (That is, there are too many different
varieties of web servers out there for us to help you install each
different one of them.) Once you have one installed, this guide will
explain how to use it to serve data using the OPeNDAP server for the
DAP.

Entire books are written about the operation of the Internet and the
WWW, and about client/server systems. This is not one of them. To
understand the OPeNDAP server's architecture, you need only understand
the following:

- A Web server is a process that runs on a computer (the host machine)
  connected to the Internet. When it receives a URL from some Web
  client, such as a user somewhere operating Netscape (or a specialized
  DAP client) it packages and returns the data specified by the URL to
  that client. The data can be text, as in a web page, but it may also
  be images, sounds, a program to be executed on the client machine, or
  some other data.
- A properly specified URL can cause a Web server to invoke a CGI
  program on its host machine, accepting as input a part of the URL, or
  some other data, and returning the output of that program to the
  client that sent the URL in the first place. The CGI is executed on
  the server.

The way the server is configured depends on the storage format of the
data you intend to serve. The OPeNDAP server supports a variety of
storage formats, including \netcdf , HDF4, and DSP. The server can also
read data using the \ffnd and \jgofs libraries, which can be configured
to read files with nearly any data format.

## Server Architecture

The OPeNDAP server consists of a set of programs, and a CGI
\new{dispatch script} used to decide which program can handle whatever
request is at hand. A user can make three different sorts of requests to
the server. The first request is for the "shape" of the data, and
consists of the data descriptor structure (DDS). The second request is
for the data attribute structure (DAS) of the data types described
\subj{You need to know the shape of the data before you request the
data.} in the DDS. \[<http://docs.opendap.org/index.php/QuickStart>\|
Quick Start Guide\] and
\[<http://docs.opendap.org/index.php/UserGuide>\| User Guide \] contain
more information about these structures.) Both of these requests return
plain text data, readable in a web browser like Netscape Navigator. You
can see a typical DDS here:

<center>

<figure>
<img src="sst.mnmean.nc.dds.gif" title="actual size" />
<figcaption>actual size</figcaption>
</figure>

A DDS (One of the basic response types defined by the
DAP,version2)\[<http://www.cdc.noaa.gov/cgi-bin/nph-nc/Datasets/reynolds_sst/sst.mnmean.nc.dds><cite>sst.mnmean.nc.dds</cite>\]

</center>

Here's a DAS from the same dataset:

<center>

<figure>
<img src="sst.mnmean.nc.das.gif" title="actual size" />
<figcaption>actual size</figcaption>
</figure>

A DAS
\[<http://www.cdc.noaa.gov/cgi-bin/nph-nc/Datasets/reynolds_sst/sst.mnmean.nc.das><cite>sst.mnmean.nc.das</cite>\]

</center>

After getting the metadata (defined, for DAP purposes, as the contents
of the DAS and DDS), the DAP client can request actual data. This is
binary data, and is often too large to view easily in a web browser.
(See \[<http://docs.opendap.org/index.php/QuickStart>\| Quick Start
Guide\] for strategies to use to examine DAP data from a standard web
browser.)

Depending on the data format in use, the DAS and DDS are either
generated from the data served, or from ancillary information you have
to supply (or both). The data in these structures may be cached by the
client system.

In addition to the three basic message types (DAS, DDS, and data), the
OPeNDAPserver can also provide information about the server operation
and about the data, can return data in ASCII comma-separated tables, and
can provide a query form allowing users to craft subsampling requests to
the server. Some of these services are provided by other service
programs that must be installed with the dispatch script and its
companions.

## More Explanation of How It Works

To understand the operation of the OPeNDAP server, it is useful to
follow the actions taken to reply to a data request. The diagrams in
[figure1](:Image:installfig1.gif "wikilink") and
[figure2](:Image:installfig2.gif "wikilink") lay out the relationship
between the various entities. Consider a DAP request URL such as the
following:

    http://test.opendap.org/opendap/nph-dods/data/nc/fnoc1.nc

The URL as written refers to the entire data file, but any particular
request must be slightly more specific. The precision is supplied by
appending a suffix to the data URL. Do you want binary data
(<font color='green'>.dods</font>), ASCII data
(<font color='green'>.asc</font>), the DDS
(<font color='green'>.dds</font>), the DAS
(<font color='green'>.das</font>), usage information
(<font color='green'>.info</font>), or a query form
(<font color='green'>.html</font>)? To get a DDS, for example, you would
use this URL:

    http://test.opendap.org/opendap/nph-dods/data/nc/fnoc1.nc.dds

A DAP client may silently add the appropriate suffix to the URL, but if
you're using a standard WWW client, such as Netscape, you have to add
the suffix yourself.

Once the proper suffix has been appended to the URL, the URL is sent out
into the world. Through the magic of IP addressing, it makes its way to
the web server (<font color='green'>httpd</font>) running on the
platform, <font color='green'>test.opendap.org</font>.
\Figureref{dods-server,fig,server-design1} shows these first steps. The
client makes an internet connection to the
<font color='green'>test.opendap.org</font> machine, and the
<font color='green'>httpd</font> daemon executes the dispatch script
(<font color='green'>opendap/nph-dods</font>) and forwards it the
remaining parts of the URL it had received
[2](Wiki_Testing/ServerInstallationGuideFootnotes "wikilink"). (In this
case, that would be <font color='green'>data/nc/fnoc1.nc.dods</font>.)
DAP requests are "GET" requests, not "POST" requests, so all the
information forwarded is in the URL
[3](Wiki_Testing/ServerInstallationGuideFootnotes "wikilink").

<center>

<figure>
<img src="installfig1.gif" title="actual size" />
<figcaption>actual size</figcaption>
</figure>

The Architecture of the OPeNDAP Server, part I.

</center>

[figure1](:Image:installfig1.gif "wikilink") illustrates what happens
next. Sitting in the CGI directory (here called
<font color='green'>opendap</font>) with the dispatch script are several
\new{service programs}, also called \new{services} or \new{helper
programs}. The dispatch script (<font color='green'>nph-dods</font>)
analyzes the suffix on the URL to figure out what kind of request this
is, and executes the corresponding service program.

> As of DODS release 3.2, the dispatch
>
> ` script is named `<font color='green'>`nph-dods`</font>` and OPeNDAP will use this name for`
> ` all of its version 3.x releases.  Earlier releases of DODS used`
> ` different dispatch scripts, depending on the storage format of the`
> ` data.  For earlier releases, there would be a `<font color='green'>`nph-nc`</font>` to handle`
> ` netCDF data, `<font color='green'>`nph-jg`</font>` to handle JGOFS data, and so on.`

In the case illustrated, the <font color='green'>.dods</font> suffix
indicates that this is a request for binary data. Therefore, the
dispatch script executes the <font color='green'>dap_nc_handler</font>
service, and forwards to it the rest of the URL, which includes a data
object name (which may be a file or not, depending on the API), and
possibly a constraint expression
[4](Wiki_Testing/ServerInstallationGuideFootnotes "wikilink"). It's up
to the service program to find the data, read it, read and parse the
constraint expression (if any), and output the data message. If the
service requires any ancillary data, it may also read an ancillary data
file or two, as necessary.

<center>

<figure>
<img src="installfig2.gif" title="actual size" />
<figcaption>actual size</figcaption>
</figure>

The Architecture of the OPeNDAP Server, part II.

</center>

The standard output of the service program is redirected to the output
of the <font color='green'>httpd</font>, so the client will receive the
program output as the reply to its request.

For APIs that are designed to read data in files, such as netCDF, the
CGI program will be executed with the working directory (also called the
default directory) specified by the <font color='green'>httpd</font>
configuration. However, the OPeNDAP software will look for its data
relative to the document root tree. On the
<font color='green'>test.opendap.org</font> server, for example, the
nph-dods CGI program is executed native to the directory
<font color='green'>/usr/local/share/dap-server/</font>, but the
document root directory is <font color='green'>/var/www/html/</font>.
The last section of the URL, then, specifies the file
<font color='green'>fnoc1.nc</font> in the directory:

    /var/www/html/data/nc/

Some existing data APIs, such as JGOFS, are not designed with file
access as their fundamental paradigm. The JGOFS system, for example,
uses an arrangement of "dictionaries" that define the location and
method of access for specified data "objects." A URL addressing a JGOFS
object may appear to represent a file, like the netCDF URL above.

    http://test.opendap.org/opendap/nph-dods/station43

However, the identifier (<font color='green'>station43</font>) after the
CGI program name (<font color='green'>nph-dods</font>) represents, not a
file, but an entry in the JGOFS data dictionary. The entry will, in
turn, identify a file or a database index entry (possibly on yet another
system) and a method to access the data indicated. These are JGOFS
server-specific installation issues covered in the installation
documentation for that server.

Note that the name and location of the CGI directory
(<font color='green'>opendap</font>), as well as the name and location
of the working directory used by the CGI programs, are local
configuration details of the particular web server in use. The location
of the JGOFS data dictionary is a configuration issue of the JGOFS
installation. That is to say these details will probably be different on
different machines.

## Service Programs

When the server gets a DAP request, it executes the dispatch script,
which then figures out which service program should be invoked. The
output from that program is what gets returned to the client.

Here is a table of the service programs required for each of the
services of the server. The dispatch script is called
<font color='green'>nph-dods</font>. (Though see note
[1.2.](Wiki_Testing/ServerInstallationGuideFootnotes "wikilink")) For
another DODS server, the names of some of the helper programs would have
a different root than <font color='green'>nc</font>. (For example,
<font color='green'>ff</font> identifies the FreeForm server,
<font color='green'>jg</font> for JGOFS, and so on.)

DODS Services, with their suffixes and helper programs. Services, with
their suffixes and helper programs. In the OPeNDAP server, different
handlers are used for each supported data access format, e.g.
<font color='green'>nc</font> for netCDF, <font color='green'>jg</font>
for JGOFS, and so on.

<center>

| Service         | Suffix                                                               | Helper Program                                                                             |
|-----------------|----------------------------------------------------------------------|--------------------------------------------------------------------------------------------|
| Data Attribute  | <font color='green'>.das</font>                                      | <font color='green'>dap_nc_handler</font>                                                  |
| Data Descriptor | <font color='green'>.dds</font>                                      | <font color='green'>dap_nc_handler</font>                                                  |
| DODS Data       | <font color='green'>.dods</font>                                     | <font color='green'>dap_nc_handler</font>                                                  |
| ASCII Data      | <font color='green'>.asc</font> or <font color='green'>.ascii</font> | <font color='green'>dap_asciival</font>                                                    |
| Information     | <font color='green'>.info</font>                                     | <font color='green'>dap_usage</font>, see ([<cite> sec,document-data</cite>](http://www)). |
| \ifh            | <font color='green'>.html</font>                                     | <font color='green'>dap_www_int</font>                                                     |
| Version         | <font color='green'>.ver</font>                                      | None                                                                                       |
| Compression     | None                                                                 | <font color='green'>deflate</font>                                                         |
| Help            | Anything else                                                        | None                                                                                       |

</center>

The service programs are started by the dispatch script depending on the
extension given with the URL. If the URL ends with \`.das' and the file
name in the URL ends in \`.nc', then the DAS service program
(<font color='green'>dap_nc_handler</font>) is started using the
<font color='green'>-O das</font> argument. Similarly, the extension
\`.dds' will cause the <font color='green'>dap_nc_handler</font> service
to be run with the <font color='green'>-O dds</font> argument, and so
on.

On the client side, when using a DAP client, the user may never see the
\`.das,' \`.dds,' or \`.dods' URL extensions. Nor will the user
necessarily be aware that each data URL given to the DAP client may
produce three different requests for information. These manipulations
happen within the DAP client software, and the user need never be aware
of them.

> This is only true *when using a DAP client* . Programs that
>
> ` don't use the OPeNDAP client libraries, or similar, can still be`
> ` clients of a DAP server. You can use Netscape to contact a DAP`
> ` server and get data, in which case you have unmoderated access to the`
> ` server and need to include the service program URL extensions.`

## Choosing a Data Handler

There are a variety of data format handlers available for the OPeNDAP
data server, each designed to handle a different data storage format.
Data handlers from OPeNDAP exist to serve data stored in the netCDF, HDF
and HDF-EOS, and DSP storage formats (other groups have built data
handlers for other formats). If you have data stored with one of these
formats, the choice is quite simple: choose the one that works with your
data.

There is also a JDBC server, written in Java, for serving data stored in
relational databases. See ([chapter
4](Wiki_Testing/ServerInstallationGuide4 "wikilink")) for more
information about installing that software. See [<cite>the DRDS Download
page</cite>](http://www.opendap.org/download/drds_server.html) for more
information about the DODS Java software.

If your data is not already stored in one of the supported formats,
don't despair. Some standard API formats include tools for translating
data into that format. For example, netCDF includes an application
called <font color='green'>ncgen</font> you can use to translate array
data into standard netCDF files by writing a data description in the
netCDF CDL (Common Data Language). See the
\[<http://www.unidata.ucar.edu/packages/netcdf/guidef/guidef-15.html#MARKER-2-3320><cite>netCDF
documentation</cite>\] for more information about
this[5](Wiki_Testing/ServerInstallationGuideFootnotes "wikilink").

If your data is not in a supported format and you don't want to
translate it into one of those formats, there is still a way to serve
your data. There are two other data handlers available that can be used
to serve data that are not already in one of these formats. These are
the FreeForm and JGOFS servers. It may be that one of these servers can
be easily adapted to your uses. The FreeForm handler is somewhat easier
to set up, and the JGOFS handler is more flexible. A key difference is
that the JGOFS handler can process data contained in several different
data files. (The FreeForm handler can, as well, but, being slightly less
flexible, it may require the files to be rearranged or renamed.)

Here's a brief comparison of the two:}

<table>
<thead>
<tr class="header">
<th><p>Server</p></th>
<th><p>Advantages</p></th>
<th><p>Disadvantages</p></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>FreeForm</p></td>
<td><p>Simple to set up. Serving data in a new format requires only</p>
<p><code>     creating a text file describing that format. Serves data in</code><br />
<code>     Arrays or Sequences.  </code></p></td>
<td><p>Not quite as flexible as its name implies. If the format in</p>
<p><code>     question is too complex or too variable, the FreeForm API cannot</code><br />
<code>     handle it.  Sequences can be served, but only flat ones.  (That</code><br />
<code>     is, Sequences that contain other Sequences will not work.)</code><br />
<code>     Generally, data must line up in columns.</code></p></td>
</tr>
<tr class="even">
<td><p>JGOFS</p></td>
<td><p>Extremely flexible. Uses specialized access methods</p>
<p><code>     to read data, and these methods can be extensively customized.</code><br />
<code>     Optimized for Sequence data (relational tables), including</code><br />
<code>     hierarchical Sequences (Sequences that contain other</code><br />
<code>     Sequences). </code></p></td>
<td><p>Writing a</p>
<p><code>     data access method can be complex, since it involves writing a</code><br />
<code>     program in C or \Cpp .  Does not support Array data types.</code><br />
</p></td>
</tr>
</tbody>
</table>

<center>

`   Advantages and Disadvantages of the Two Flexible DODS Servers`

</center>

It is possible that none of these options is the right one for you, in
which case you can use the OPeNDAP DAP library to craft a server of your
very own. The library is available in both C++ and Java. If you choose
this route, contact OPeNDAP; we may be able to direct you to someone who
has already done something like it. The [<cite>Programmer
Guide</cite>](http://docs.opendap.org/index.php/ProgrammerGuide)
contains useful information about the DAP library, including
instructions on how to construct servers and clients.